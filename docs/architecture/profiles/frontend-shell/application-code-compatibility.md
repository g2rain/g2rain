# 基于应用编码的双模式子应用兼容方案

适用范围：所有采用中央 `frontend-shell` Profile 的 Main Shell（含 `g2rain-main-shell`、由 `create-g2rain-app shell` 生成的业务壳，以及参考实现 `g2rain-admin-shell`）。本文件是跨仓升级指南，不替代 [Main Shell 契约](main-shell-contract.md) 与 [生命周期与跨应用契约](shell-runtime-policy.md)。

## 1. 背景与目标

新主应用 Shell 需要在同一个工作区内承载两类 qiankun 子应用：

| 代表应用 | 接入模式 | 说明 |
| --- | --- | --- |
| `g2rain-member-app` | `appkit` | 使用 `@g2rain/platform/sub` 的新契约，要求显式身份上下文与定向消息。 |
| `g2rain-manager-app` | `legacy` | 使用现有主壳兼容字段与传统事件约定，迁移前必须保持行为不变。 |

本方案的目标不是让子应用选择宿主，也不是为每一个业务应用复制一份分支逻辑。目标是由 Shell 在最少的位置吸收代际差异，使新增应用只依赖 AppKit 契约，存量应用能按计划迁移。

范围包括：应用识别、定义注册、公共运行上下文、认证消息、路由与生命周期、可观测性、灰度和下线。业务页面、IAM 的签发规则、Gateway 的最终鉴权不在范围内。

## 2. 架构决策

1. `applicationCode` 是模式选择的唯一键。禁止根据 `entry`、域名、菜单名称、路由前缀或是否有某个全局变量猜测模式。
2. Shell 维护模式适配；子应用不得导入 Shell 私有实现或自行分辨具体壳仓库名称。
3. 菜单服务仍是应用可见性、权限、`entry`、子应用 `contextPath`、菜单 `linkPath` 的事实来源。静态应用编码配置**只登记 legacy**，不能复制菜单或权限；未登记的编码默认 `appkit`。
4. AppKit 是唯一演进目标与默认模式。`legacy` 只用于已存在的应用兼容，不能成为新应用默认模板，也不得要求新应用写入配置表。
5. 两类应用当前都可由 qiankun 装载。模式表示**协议适配**，不是第二套菜单、Tab 或路由系统。
6. Token、Token Kid、client 私钥和 Secret 不进入应用编码配置、公开 props、URL、持久化菜单数据或日志。认证仅经受校验的定向 Auth Bridge 协调。

> 注意：部分存量壳（如早期 `g2rain-main-shell`）可能仍采用「未登记默认 legacy」或在 props 中下发 Token。升级到本方案时必须以「未登记默认 appkit、仅登记 legacy、appkit 公开 props 无 Token」为准，并在项目 `docs/architecture/deviations.md` 登记迁移期偏差。

## 3. 应用编码配置

建议新增 Shell 私有配置 `src/platform/apps/application-code.config.ts`。它只登记需要兼容的 **legacy** 应用；**appkit（新模式）应用不写入此表**。

```ts
export type AppIntegrationMode = 'appkit' | 'legacy';

export interface ApplicationCodeConfig {
  mode: 'legacy';
  protocolVersion: 'legacy';
}

/** 仅配置仍走旧契约的应用；未出现在此表中的 applicationCode 一律按 appkit 处理。 */
export const LEGACY_APPLICATION_CODE_CONFIG = {
  'g2rain-manager-app': { mode: 'legacy', protocolVersion: 'legacy' },
} as const satisfies Record<string, ApplicationCodeConfig>;
```

配置规则：

- 仅以服务端菜单返回的 `applicationCode` 查询本表；命中则 `MicroAppDefinition.mode = 'legacy'`，未命中则默认为 `appkit`（`protocolVersion: '1'`）。
- **不要**为 `g2rain-member-app` 等新模式应用增加配置条目；默认即 AppKit 契约。
- 同一 `applicationCode` 只能有一个模式。将存量应用迁出 legacy 时，从本表删除对应条目即可切回默认 `appkit`；不允许由用户、URL 参数或子应用脚本覆盖。
- 不得在此配置中记录 Token 或整份菜单响应；告警日志仅可含脱敏的 `applicationCode` 与原因。
- 本地联调的子应用 `entry` / `endpointUrl` 由菜单或 Mock 提供，**不要**在壳源码中维护 DEV entry 覆盖表。

## 4. 身份、定义与实例模型

四种标识不可混用：

| 字段 | 归属与稳定性 | 用途 |
| --- | --- | --- |
| `applicationCode` | 应用级稳定身份 | 查询模式配置、认证消息目标、应用级可观测性。 |
| `viewId` | Shell 工作区视图身份 | 区分同一应用打开的不同菜单/视图。 |
| `instanceId` | 一次运行实例身份 | qiankun handle、操作队列、容器、路由状态和定向消息的目标。 |
| `appKey` | 迁移期兼容别名 | 当前等于 `instanceId`；不得再用于表达稳定应用身份。 |

`MicroAppDefinition` 至少应包含：

```ts
interface MicroAppDefinition {
  applicationCode: string;
  mode: AppIntegrationMode;
  name: string;
  entry: string;
  contextPath: string;
  activeRule: string;
}
```

其中 `name === applicationCode` 仍用于 vite-plugin-qiankun 的运行时命名；不得把 `instanceId` 拼入 `name`。工作区打开子应用时生成 `instanceId = \`${applicationCode}:${viewId}\``，并在迁移期同步给 `appKey`。

## 5. 运行上下文与认证边界

Shell 为所有模式构造相同的、无敏感信息的公开 props。新模式以这些字段为正式契约；旧模式可以忽略未识别字段。

```ts
{
  applicationCode,
  viewId,
  instanceId,
  appKey,             // legacy 兼容，等于 instanceId
  contextPath,        // 子应用的 contextPath，绝不是 Shell 的 VITE_CONTEXT_PATH
  activeRule,
  entryOrigin,
  initialRoute,
  locale,
}
```

以下内容不属于公开 props：`token`、`tokenKid`、`client`、私钥、刷新令牌、SSO code、用户凭据。子应用在 `mount` 前或令牌失效后发送类型化定向消息；Shell 校验消息的 `applicationCode`、`viewId`、`instanceId` 与当前 RuntimeInstance 一致，调用壳侧 `ensureAccessToken` / `refreshToken` 后，只向该实例响应。`legacy` 适配器负责把旧消息名或负载转换为这条内部流程；转换不能分散在菜单、Workspace 或业务应用中。

> 迁移期例外：为兼容仍依赖 `initTokenFromProps` 的 legacy 子应用（如 Manager），`legacyAdapter` 可在 mount/update 时于公开 props **之外**合并 session 的 `token` / `tokenKid` / `client`。该行为必须登记项目偏差，且不得扩展为 appkit 或新应用默认。

## 6. 适配器边界

运行时保持一个对 Shell 统一的接口：

```ts
interface MicroAppRuntimeAdapter {
  mount(instance: RuntimeInstance): Promise<void>;
  update(instanceId: string, patch: PublicContextPatch): Promise<void>;
  unmount(instanceId: string): Promise<void>;
  destroy(instanceId: string): Promise<void>;
}
```

`RuntimeStore` 根据 `definition.mode` 选择 `appkitAdapter` 或 `legacyAdapter`。二者都复用 Shell 所有的实例表、per-`instanceId` 队列、qiankun handle 存储和 Workspace 容器；不得各自维护一套 Tab、路由或 Token Store。

| 责任 | `appkitAdapter` | `legacyAdapter` |
| --- | --- | --- |
| 身份 props | 强制传递四个显式字段 | 同时传递显式字段与 `appKey`。 |
| 生命周期 | 调用 AppKit 标准子应用生命周期 | 调用现有 qiankun 生命周期并封装差异。 |
| 消息 | 使用版本化、定向的消息信封 | 映射旧事件到同一内部处理器。 |
| 认证 | 请求式 Auth Bridge | 保持既有方式，过渡到同一 Auth Bridge。 |
| 更新 | 仅目标实例的公开上下文 patch | 转为旧应用可识别的 update；不下发敏感信息到日志/URL。 |

初期两个适配器可以复用同一个 qiankun `loadMicroApp` 实现；只有 props 映射和消息桥不同。未来替换 qiankun 或引入其他运行时，也必须保持这个边界不变。

## 7. 生命周期、Tab 与路由

```text
菜单 applicationCode
  → 查模式配置 → 注册 MicroAppDefinition
  → 打开 viewId → 创建 instanceId / RuntimeInstance
  → 渲染常驻容器 → 选择适配器 → mount
  → 定向认证、Locale、initialRoute 协同
  → 切 Tab：标记 inactive + v-show
  → 关 Tab：unmount → 删除 handle → releaseInstance → 删除实例与 Tab
```

- 定义以 `applicationCode` 去重，实例以 `instanceId` 去重；禁止用 `appKey` 查找应用定义。
- 切换 Tab 不调用 `unmount`、`remount` 或 `update(initialRoute)`，只隐藏容器并标记 `inactive`。
- 关闭 Tab 时严格按 `unmount`、移除 qiankun handle、`releaseInstance`、删除 RuntimeInstance、删除 Tab 的顺序执行；任一步失败仍必须执行可安全的后续清理并上报失败状态。
- `initialRoute` 是子应用内部路径。`contextPath` / `activeRule` 是部署和浏览器激活前缀，不能互相替代。
- 子应用的路由变化必须带目标实例身份。Shell 只接受当前实例的消息，并以 `history.replaceState` 更新浏览器地址，避免 Router 重定向回 Shell 首页。
- 同一应用的两个实例必须互不影响：一个实例更新 Locale、关闭或挂载失败，不得重置另一个实例的 Router、认证等待或 DOM 容器。

## 8. 迁移阶段与回滚

### 阶段 A：建立兼容边界

新增配置、`mode` 字段、显式身份字段和模式分发，但不改变 Manager 的既有消息和路由行为。以 `g2rain-manager-app` 验证回归。

### 阶段 B：接入 AppKit 代表应用

确认 `g2rain-member-app` **未**出现在 legacy 配置表中（默认 `appkit`），使用 `@g2rain/platform/sub` 验证 mount、update、unmount、深链、多个实例与请求式认证。

### 阶段 C：灰度与扩展

按 `applicationCode` 开启应用，先在开发/测试环境验证，再逐个迁移存量应用。每个 legacy 应用须登记验证记录与退出日期；迁出时从 legacy 配置表删除该编码。

### 阶段 D：收敛

迁移完成后删除该应用的旧消息映射与 `appKey` 依赖，并从 legacy 配置表移除条目。全部应用完成迁移后，清空 legacy 配置、拒绝 `legacy` 模式，并删除 legacy adapter。

回滚只需把该应用编码重新写入 legacy 配置表，或禁用其新入口；不得通过回滚 Shell 全局路由、认证或其他应用实现来处理单一应用故障。若 AppKit 与旧契约数据不兼容，应保留旧适配器并停止该应用迁移，直到实现可逆转换。

## 9. 验收与可观测性

每次协议或配置变更至少完成下列验证：

| 场景 | Member（appkit） | Manager（legacy） |
| --- | --- | --- |
| 首次挂载与失败重试 | 必须通过 | 必须回归通过 |
| 深链刷新 / SSO 回跳 | 必须通过 | 必须回归通过 |
| 两个同应用实例 | identity 与 Router 隔离 | 不得因兼容层串扰 |
| Tab 切换与关闭 | 不卸载；关闭后完整清理 | 不卸载；关闭后完整清理 |
| Token 请求、失效刷新 | 仅目标实例收到响应 | 旧事件映射后仅目标实例收到响应 |
| Locale / 公共上下文 update | 只更新目标实例 | 不破坏旧应用 |

日志与指标只能使用 `applicationCode`、`mode`、`protocolVersion`、`viewId`、`instanceId`、生命周期阶段和脱敏错误码；严禁记录 token、client、完整消息负载或菜单响应。未命中 legacy 配置表时按默认 `appkit` 处理即可，不必告警；模式/子应用声明冲突、重复 mount、未清理 handle、无效目标消息必须产生可诊断事件。

浏览器联调至少使用真实 `g2rain-member-app` 与 `g2rain-manager-app` 各一次；生产构建、文档链接检查、菜单入口/Context Path/Nginx 深链回退检查为发布前置条件。

## 10. 实施清单

1. 创建 **仅含 legacy** 的应用编码配置，以及 `AppIntegrationMode` / `MicroAppDefinition.mode` 类型；未配置编码默认 `appkit`。
2. 在菜单转换与 `registerDefinition` 时按配置表解析模式（命中 legacy / 未命中默认 appkit），并增加唯一性与路径校验。
3. 在 RuntimeStore 构造统一公开 props，补全显式身份字段，同时保留 `appKey`。
4. 将现有 qiankun 调用收口为 `appkitAdapter`、`legacyAdapter` 的共有实现与两层协议映射。
5. 将旧 Token、路由和 Locale 事件映射收口到 `legacyAdapter`；不得改写业务子应用。
6. 以 Member、Manager 完成上述验收矩阵与灰度/回滚演练。
7. 在每个存量应用迁移后移除其专属兼容代码，并更新偏差表和迁移记录。

## 11. 参考实现

- 参考落地：`g2rain-admin-shell`（默认 appkit、仅 legacy 表、双 adapter、legacy 消息桥）。
- 权威契约：本 Profile 下的 [Main Shell 契约](main-shell-contract.md)、appkit `@g2rain/platform/main` / `@g2rain/platform/sub`。
- 项目级临时偏差写在各壳仓库的 `docs/architecture/deviations.md`，不得把迁移期例外写成中央默认范例。
