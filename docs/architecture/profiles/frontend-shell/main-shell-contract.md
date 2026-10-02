# Main Shell 契约

本文是 AI 助手、生成器与人工实现编写 / 改造 Main Shell 时的**中央规范事实来源**。它定义不变式、所有权边界与完成检查；具体的 appkit 安装与公开 API 细节由 `g2rain-appkit` 维护。

| 文档 | 职责 |
| --- | --- |
| **本文** | 必须 / 禁止、身份模型、生命周期、docs 最小树与完成检查 |
| [frontend-shell Profile](README.md) / [Shell 生成规范](shell-generation-policy.md) | Profile 级边界与需求输入 |
| [Appkit Main Shell 接入](https://github.com/g2rain/g2rain-appkit/blob/main/docs/integration/main.md) | `npm pack`、安装与接线步骤 |
| [Appkit Main Shell 提示词](https://github.com/g2rain/g2rain-appkit/blob/main/docs/integration/main-shell-generation-prompt.md) | 可复制任务提示词；必须引用本文 |
| [Appkit HTTP 与 Runtime 契约](https://github.com/g2rain/g2rain-appkit/blob/main/docs/packages/http-runtime-contract.md) | `@g2rain/http` 公开边界与应用装配分工 |

CLI 可通过 `create-g2rain-app shell` 从独立模板仓 `g2rain-shell-template` 生成 Main Shell 基线；**禁止**用 `create-g2rain-app app` / `g2rain-app-template` 冒充 Shell。已生成的 `g2rain-admin-shell` 与模板共享同一运行时基线，可用于验证生成结果；`g2rain-main-shell` 是存量主应用迁移与兼容性参考。跨仓约束以本 Profile 与本文为准。

权威公开 API 以 `@g2rain/platform`、`@g2rain/http` 的 `package.json#exports`、类型声明与包源码为准。appkit 包的 API 变化必须同步评估本文；本文不得复制 appkit 的 API 正文。

---

## 1. 角色与所有权

### 1.1 Main Shell 拥有

- Shell 本地视图、工作区 / Tab 状态与微应用容器的生命周期；本文不规定 Header、Sidebar 或其他页面布局
- `MicroAppDefinition`、`WorkspaceView`（现行实现可为 Tab）、`RuntimeInstance` 注册表
- 每 `instanceId` 的生命周期操作队列（mount / update / unmount / destroy 串行）
- RuntimeAdapter（现行：qiankun `loadMicroApp`、`MicroApp` handle、唯一运行名与 container）
- Shell 路由、菜单、SSO 回调、Token Store、登出与会话协调
- HTTP Client 单例表、Mock、环境 Base URL、刷新屏障等应用层装配（`runtime/http`）
- Nginx / 运行时环境配置与静态资源

### 1.2 `@g2rain/platform/main` 拥有

- 公开 props 快照与 `buildPublicProps` / `updatePublicContext`
- 按实例定向的消息信封（`MainDirectedMessage`）
- `notifyLocale`、`notifyAuthInvalid`、`emitToInstance`、`releaseInstance`

### 1.3 `@g2rain/http` 拥有

- Client 工厂、序列化、DPoP 纯函数、标准错误模型与通用拦截器能力
- 通过工厂参数接收的 `authSessionProvider` / `ensureAccessToken` / `authErrorHandler` 钩子（由壳注入）

### 1.4 Platform Main 与 HTTP **不**拥有

- Vue、Pinia、Vue Router、Element Plus 或 Shell UI
- `loadMicroApp` / `registerMicroApps` / qiankun handle
- RuntimeStore、菜单、Tab、**Token Store** 或壳侧 HTTP Client 注册表
- 子应用业务页面、领域 API、权限数据源

### 1.5 子应用拥有

- 自己的 Vue / Router / Pinia / HTTP / 业务资源
- qiankun 子应用入口与 `@g2rain/platform/sub` 生命周期
- Auth Bridge（如何从消息或一次性 auth 取得 Token）；Kernel 不读 Token

---

## 2. 依赖与层次（实现壳时必须遵守）

目标依赖方向（`g2rain-main-shell` Profile）：

```text
shared → components → platform → runtime → views / shell
组合根 main.ts / App.vue 可装配各层
```

强制规则：

1. 不把子应用业务规则写入 Shell。
2. 不新增 `components` → `platform`/`runtime`、`platform` → `runtime` 的反向依赖；已有偏差须登记并不得作为范例。
3. 跨模块只从稳定 `index.ts` 公共出口导入。
4. 消费 `@g2rain/platform` / `@g2rain/http` 时**只**从各自 `package.json#exports` 入口导入；禁止深路径进 `src`/`dist` 内部。
5. Main Shell **基线依赖**为 `@g2rain/platform`（`/main`、`/theme`）、`@g2rain/http`、`@g2rain/ui` 与 `@g2rain/theme`。组合根须导入 Theme / UI 样式、安装 UI 适配，并创建唯一主题控制器；主题持久化属于 Shell，`@g2rain/theme` 只提供主题资源。本文不规定视觉布局或组件选型。壳负责 SSO 与 Token 会话，HTTP Client、拦截器、DPoP 与 `ensureAccessToken` 钩子必须走公共 HTTP 包，并在组合根注入壳的 Token Store / 刷新编排；接入后收敛或删除本地 `components/http` 副本。
6. Token、Token Kid、私钥、生产 Secret **不得**写入源码、Mock、生成模板、公开 props、URL 或持久日志。Token Store 与消息换票仍属壳层，**不得**下沉进 `@g2rain/http` 或 `@g2rain/platform`。

---

## 3. 文档与项目元数据（生成必须产出）

新建或脚手架级改造 Main Shell 时，**必须同时生成符合中央约束的 `docs/`**，不能只交源码。文档结构权威来源：

1. 中央 `frontend-shell` Profile：`requiredDocuments`（`profile.yaml`）
2. 中央 [Shell 生成与需求输入规范](https://github.com/g2rain/g2rain/blob/architecture-v1.2.0/docs/architecture/profiles/frontend-shell/shell-generation-policy.md) 第 3 节「生成输出契约」
3. 基础 Profile `frontend-app` 的文档与偏差规则（Shell 继承未覆盖部分）

不得自创平行文档体系（例如把架构说明只写在根 README、或用 `docs/guide/` 替代 `docs/architecture/`）。专题可追加文件，但**不得缺省**中央必填路径，且须在 `docs/index.md` 与 `docs/project.yaml` 的 `documentation` 中可导航。

### 3.1 最小文档树

AI 创建的新 Shell 至少输出：

```text
AGENTS.md
docs/project.yaml
docs/index.md
docs/architecture/overview.md
docs/architecture/layers.md
docs/architecture/dependencies.md
docs/architecture/runtime-flows.md
docs/architecture/deviations.md
docs/development/local-development.md
docs/development/testing.md
docs/development/definition-of-done.md
docs/operations/configuration.md
docs/operations/deployment.md
docs/operations/troubleshooting.md
docs/security/security-boundaries.md
docs/requirements/README.md
src/{shared,components,platform,runtime,views,shell}/
```

与中央 `frontend-shell` `requiredDocuments` 对齐时注意：Profile 必填含 `AGENTS.md` 与上表 architecture / development（testing、definition-of-done）/ operations（configuration、deployment）/ security / requirements；生成规范额外要求 `docs/index.md`、`local-development.md`、`troubleshooting.md`。**两者并集均须生成。**

### 3.2 `docs/project.yaml` 必填语义

至少记录并与实现一致：

- `family: frontend-shell`（或等价角色字段）
- 中央仓库、`baselineRef` / 架构快照、`frontend-app` 与 `frontend-shell` Profile 路径与版本、`deviations` 路径
- `runtime`：`applicationCode`、`contextPath`、端口、子应用契约字段（至少含 `appKey`/`name`/`entry`/`activeRule`/`instanceId`，并规划 `applicationCode`/`viewId`）
- `commands`：含可执行的 `verify`（通常 `npm run build`）
- `documentation.entry` → `docs/index.md`；`documentation.agentEntry` → `AGENTS.md`
- `aiCoding`：需求模板路径与校验步骤；无活跃需求时 `activeRequirement` 为 `null`

接入 Appkit 时还应索引：`platformMainAdoption`（或等价）指向本仓 Platform Main / HTTP 采纳说明。

### 3.3 各文档写什么（禁止空壳凑数）

| 路径 | 必须写清 |
| --- | --- |
| `AGENTS.md` | Agent 阅读顺序、Shell 边界、契约字段、验证命令；指向中央 Profile 与本文 |
| `docs/index.md` | 导航到下列全部必填页；相对链接有效 |
| `architecture/overview.md` | Shell 职责与非职责 |
| `architecture/layers.md` | `shared → … → views/shell` 目标方向 |
| `architecture/dependencies.md` | 依赖规则与外部协作仓 |
| `architecture/runtime-flows.md` | 启动、子应用生命周期、认证/消息、部署路径 |
| `architecture/deviations.md` | 已知偏差表（可先为空表 + 说明）；不得省略文件 |
| `development/local-development.md` | 本地启动、环境变量、与子应用联调方式 |
| `development/testing.md` / `definition-of-done.md` | 测试策略与完成定义 |
| `operations/*` | 配置、部署、排障 |
| `security/security-boundaries.md` | Token、entry、密钥、公开配置边界 |
| `requirements/README.md` | 需求入口；可指向中央 Shell 需求模板 |

### 3.4 Appkit 相关专题（在中央树之上追加）

在已满足第 3.1 节的前提下，接入 `@g2rain/platform/main` / `@g2rain/http` 时**应追加**（改造现有壳时同理）：

- `docs/development/platform-main-adoption.md`：本仓采纳状态、改造锚点、联调对象
- `docs/index.md` 与 `project.yaml` 增加对应链接/索引

专题正文**不得**复制 appkit 公开 API；链到本文与 [Appkit Main 接入手册](https://github.com/g2rain/g2rain-appkit/blob/main/docs/integration/main.md)。

### 3.5 文档一致性检查

生成或改造完成后必须：

- 校验 `docs/**/*.md` 相对链接可解析
- `docs/index.md` 覆盖第 3.1 节必填页
- `project.yaml` 中的 Profile 版本、Context Path、端口、契约字段与源码/部署配置一致
- 未完成的 Main/Sub、安全或部署事项写入 `deviations.md`，不得用临时代码或空文档假装完成

---

## 4. 身份模型（跨应用契约）

三个字段语义分离，禁止混用：

| 字段 | 含义 | 权威所有者 |
| --- | --- | --- |
| `applicationCode` | 实际子应用稳定标识（应用定义级） | `MicroAppDefinition` |
| `viewId` | 工作区打开的页面视图（Tab / WorkspaceView） | Shell Workspace |
| `instanceId` | 一次运行实例的唯一键 | `RuntimeInstance` |

迁移与定向规则：

- 新协议：`appKey` **固定等于** `instanceId`（见 `createMainPlatform`）。
- 第一阶段允许三者局部 ID 数值相等，但协议字段名不得互相顶替语义。
- 定向消息必须带可校验的 `instanceId`（及迁移期兼容所需字段）；响应绑定 `requestId` 与目标实例。
- 子应用侧迁移期可读 `instanceId ?? appKey`；Main Shell 接入 `/main` 后应显式下发 `applicationCode`、`viewId`、`instanceId`。

菜单 / 子应用定义至少校验并稳定传递：`appKey`（迁移）、`name`、`entry`、`activeRule`、`instanceId`。`entry` 只允许可信来源，禁止未校验的 URL 参数直接控制。

---

## 5. 公开 Props 契约

挂载子应用前，必须通过 `createMainPlatform().buildPublicProps(context, host)` 得到公开 props，再交给现有 `loadMicroApp`。

### 5.1 必须提供的 Context

```ts
{
  applicationCode: string  // 非空
  viewId: string           // 非空
  instanceId: string       // 非空
  mode: 'integrated'
  contextPath: string      // 子应用路由前缀，不随单次 update 改变
  locale?: string
  initialRoute?: string
}
```

Host 附加（不写入 `RuntimeContext`）：

```ts
{ activeRule?: string; entryOrigin?: string }
```

### 5.2 公开 props 允许字段（`MainPublicProps`）

- `applicationCode`、`viewId`、`instanceId`、`appKey`（= `instanceId`）
- 可选：`locale`、`initialRoute`、`activeRule`、`entryOrigin`

### 5.3 禁止放入 `buildPublicProps` 参数或返回值

- `token`、`tokenKid`、`client`、私钥、Secret
- `theme` / `metadata`（当前 Main 协调器不下发；主题协作未纳入本契约首版）
- 任意第二套未文档化的全局 window 变量作为正式契约

同一 `instanceId` 再次 `buildPublicProps` 会覆盖该实例公开快照。未先 `buildPublicProps` 就 `updatePublicContext` / `notifyLocale` / `emitToInstance` 会失败（`runtime.main.missing-snapshot`）。

---

## 6. Platform Main 接线不变式

在组合根创建**一次**协调器；`runtimePort` 委托给壳已有能力，**禁止**在 platform 内再包一层 `loadMicroApp`。

```ts
import { createMainPlatform } from '@g2rain/platform/main'

const platform = createMainPlatform({
  runtimePort: {
    async updateInstanceProps(instanceId, props) {
      // 必须 await qiankun microApp.update()，并纳入该 instanceId 操作队列
      await qiankunAdapter.updateInstanceProps(instanceId, props)
    },
    emit(message) {
      eventAdapter.emit(message)
    },
  },
})
```

| 时机 | 必须调用 |
| --- | --- |
| 打开 / 首次挂载实例 | `buildPublicProps` → `loadMicroApp({ props })` |
| 语言或初始路由变化 | `notifyLocale` 和/或 `updatePublicContext` |
| 需要通知该实例认证失效 | `notifyAuthInvalid(instanceId)`（发送 `g2rain:sub-app:token-invalid`） |
| 其他定向消息 | `emitToInstance(instanceId, type, data)` |
| qiankun `unmount` 完成并删除 handle 之后；登出清空全部实例 | `releaseInstance(instanceId)` |

`updatePublicContext` 的 `patch` 中写成 `undefined` 的字段表示从已下发 props **删除**，不是保留旧值。端口调用成功后才提交本地快照。

---

## 7. 生命周期与运行时行为

### 7.1 启动顺序

运行时初始化按以下依赖顺序完成；UI 的呈现顺序与页面布局不属于本契约：

1. 读取运行时环境配置，并在 Router 创建前将微应用深链改写到 Shell Redirect Gateway。
2. 创建 Vue App、Pinia、i18n / `@g2rain/ui`，初始化唯一 `@g2rain/theme` 控制器。
3. 初始化 `@g2rain/http` Client；注入 Shell 的会话、刷新和认证失败处理。任何需登录的请求不得早于此步骤。
4. 创建唯一 `createMainPlatform({ runtimePort })`；注册定向消息派发、`REQUEST_TOKEN`、`TOKEN_INVALID` 与 `route-change` 处理器。
5. 启动 qiankun Adapter（`singular: false`）并注册 Shell Router、SSO 与会话恢复。
6. 登录态稳定后加载菜单、注册可信 `MicroAppDefinition`，再执行深链 / SSO 回跳恢复。
7. 挂载根应用；首次打开、切换和关闭工作区视图只能走本节后续定义的状态机。

### 7.2 菜单到运行时定义

菜单是打开视图的输入，不是 RuntimeInstance 注册表。实现必须先把菜单规范化，再生成工作区视图：

| 菜单类型 | 行为 | 不得做的事 |
| --- | --- | --- |
| `shell` | 打开 Shell 本地 `WorkspaceView` | 不得创建微应用实例 |
| `group` | 仅分组，可递归包含菜单 | 不得作为可挂载叶子 |
| `sub` | 由可信后端菜单注册子应用定义，并打开微应用 `WorkspaceView` | 不得直接把未经校验的菜单值交给 `loadMicroApp` |

`sub` 菜单叶子至少提供 `applicationCode`（或迁移字段 `name`）、`entry`、`activeRule` / `contextPath`、稳定 `menu key` 与子应用内部 `routePath`。注册规则如下：

1. 先以前端内置 Shell 菜单作为稳定入口，再追加已登录用户的授权菜单；未登录或菜单加载失败时仍可保留 Shell 本地入口。
2. 按 `applicationCode` 去重并注册 `MicroAppDefinition`；`name` 必须规范化为 `applicationCode`，以匹配 vite-plugin-qiankun 生命周期名。
3. `entry` 由受信目录中的 origin 与规范化 `contextPath` 合成，必须是可解析的应用入口；`activeRule` 与 `contextPath` 是部署 / 激活前缀，不能拿 `routePath` 代替。
4. 菜单 `routePath` 映射为本次打开的 `initialRoute`，必须以 `/` 开头；缺失或非法时拒绝打开，不得悄悄回退到任意首页。
5. 登出、租户 / 机构切换或授权菜单刷新时，先清理旧定义与运行实例，再重新加载；不得让旧菜单继续打开已撤销的子应用。

### 7.3 子应用实例

1. 菜单就绪 → 提取并校验定义 → 注册 `MicroAppDefinition`
2. 打开 WorkspaceView / Tab → 分配 `instanceId` → 渲染独立 container
3. `buildPublicProps` → Adapter `loadMicroApp` → 等待 mount
4. 同 `instanceId` 上 mount / update / unmount / destroy **串行**；`updateInstanceProps` **必须** `await microApp.update()`
5. 切走 Tab：壳可将状态标为 `inactive`，**不**调用子应用 `unmount`
6. 关闭 Tab / destroy：先 Adapter unmount 并删 handle，再 `releaseInstance`，再移除 RuntimeInstance

现行基线：`singular: false`、每实例唯一 container 与 qiankun handle、稳定运行名 `name === applicationCode`、仅开启必要的样式隔离策略；具体 sandbox 选择属 Adapter 实现细节，不得泄漏进 Platform 公共协议。

### 7.4 工作区与路由协同

- 工作区打开时按 `applicationCode + viewId` 去重；推荐稳定生成 `instanceId = ${applicationCode}:${viewId}`，并令迁移字段 `appKey === instanceId`。`loadMicroApp.name` 始终是 `applicationCode`，不得拼接 `instanceId`。
- 每个已打开微应用 Tab 必须拥有独立且常驻的 container；切 Tab 只变更活跃状态和可见性，不得销毁 container 或重新 mount 已挂载实例。
- 首次 mount 失败时必须同时回滚 qiankun handle、Platform 快照、RuntimeInstance 与失败 Tab，使用户可以再次打开该菜单重试。
- 子应用上报内部路由变化；壳更新该实例的 `lastActivePath`（或等价字段）并用 `history.replaceState` 同步地址栏时，避免触发主 Router 导航循环。
- 忽略非当前激活实例的迟到路由消息，防止 Tab 串台。
- 深链 / SSO 回调后的恢复由壳导航模块负责，不放入 Platform。微应用深链应先进入 Shell Redirect Gateway，待登录且菜单定义就绪后，解析为受信 `WorkspaceView` 再打开；不得由 URL 直接指定 `entry`。

### 7.5 认证与消息

| 事件 | 方向 | 壳侧要求 |
| --- | --- | --- |
| `g2rain:sub-app:route-change` | Sub → Main | 校验目标实例；更新路径；防循环 |
| `g2rain:sub-app:token-invalid` | Sub → Main | 刷新会话；按实例响应；或由壳决定全局登出 |
| `g2rain:main-app:token-response` | Main → Sub | 绑定 `requestId` 与目标；**不**自监听本窗派发 |
| `g2rain:sub-app:request-token` | Sub → Main | 若声明支持则必须实现处理器；不得只写类型不接线 |

Token 交换留在壳 Auth Bridge / 消息层，**不**经 `buildPublicProps`。壳侧请求与刷新必须经 `@g2rain/http` 装配的 Client（注入壳的 session / `ensureAccessToken`），与子应用消息换票同一套会话真相。未来可收敛为纯 REQUEST_TOKEN / TOKEN_RESPONSE；在未实现请求处理器前，不得在文档或生成代码中假装已支持。

消息通道若基于 `window` 事件，必须具备来源、实例与请求关联校验计划（见壳侧安全偏差）；生成代码不得引入「任意同页脚本即可要 Token」的默认实现。

---

## 8. 禁止生成的模式

AI / 生成器**不得**：

1. 在 `@g2rain/platform` 内调用或重新封装 `loadMicroApp`
2. 把 Token 写入公开 props、URL、query、hash 或本地可枚举日志
3. 用菜单 `key`、路由 path 或 `applicationCode` 冒充 `instanceId` 语义后却不下发真实 `instanceId`
4. 切 Tab 时对子应用调用 `unmount`（除非产品明确改为销毁实例）
5. `update` 时忽略 Promise / 不入 per-instance 队列
6. 关闭实例或登出后不调用 `releaseInstance`，导致公开快照泄漏
7. 在 Shell 中复制第二份 RuntimeInstance 注册表到 Platform
8. 把 IAM / Gateway 鉴权或子应用业务校验下沉进 Shell「图方便」
9. 将生产私钥、`.pem` 密钥或 Secret 写入仓库、镜像构建上下文或前端 Bundle
10. 静默发明第二套消息 type 或 props 字段而不更新契约文档与类型
11. 只生成源码不生成第 3 节必填 `docs/`，或另起平行文档目录替代中央 `frontend-shell` 树
12. 用业务子应用（`frontend-app`）模板 / CLI 产物冒充 Shell，或省略 `AGENTS.md` / `docs/project.yaml`

---

## 9. 存量 `g2rain-main-shell` 的迁移差距（改造时必须处理）

以下是存量参考实现接入 `/main` 前的已知差距，不是新 Shell 的可选能力。新生成的 Shell 应以第 7 节运行时基线实现；改造旧代码时优先闭合，而不是复制旧行为：

| 缺口 | 目标 |
| --- | --- |
| 未安装 `@g2rain/platform` / `@g2rain/http` / `@g2rain/ui` / `@g2rain/theme` | 按 [Appkit Main 接入手册](https://github.com/g2rain/g2rain-appkit/blob/main/docs/integration/main.md) 安装并在组合根接线 |
| 未创建 `createMainPlatform` | 组合根单例 + `runtimePort` |
| 仍用本地 `components/http` | 迁到 `@g2rain/http`；装配留在 `runtime/http` |
| `buildInstanceFromTab` 手写 props 且含 Token | 改为 `buildPublicProps`；Token 走消息或 Auth Bridge |
| 缺少显式 `applicationCode` / `viewId` | 从定义与 WorkspaceView 填入 |
| `REQUEST_TOKEN` 仅有类型无 Handler | 实现或从对外承诺中删除 |
| `updateInstanceProps` 未统一 await + 入队 | 修复后作为 runtimePort |
| destroy / logout 无 `releaseInstance` | 在 unmount 后调用 |
| 联调对象文档写 manager-app，appkit 试点为 member-app | 联合验收优先已接 `/sub` 的 Member；契约变更仍可用 Manager 回归 |

当前 appkit 接入状态以 [Appkit 接入就绪清单](https://github.com/g2rain/g2rain-appkit/blob/main/docs/integration/readiness.md) 为准；中央 Profile 不替代包级发布和联调状态。

---

## 10. AI 执行清单（生成或改造完成后自检）

- [ ] 第 3 节必填文档树齐全；`docs/index.md` 可导航；相对链接有效
- [ ] `docs/project.yaml` 固定 `frontend-app` + `frontend-shell` Profile 版本与中央快照；与 Context Path / 端口 / 契约字段一致
- [ ] `AGENTS.md` 阅读顺序含中央 Profile、本地偏差与本文（及本仓 Platform 采纳说明）
- [ ] 依赖方向符合第 2 节；无新增反向依赖范例
- [ ] 已安装并使用 `@g2rain/platform/main`、`@g2rain/http`、`@g2rain/ui` 与 `@g2rain/theme`；`createMainPlatform` 与主题控制器均仅在组合根创建一次
- [ ] HTTP 经公共包工厂装配；Token Store / SSO / 刷新编排仍在壳；本地 `components/http` 已收敛或删除
- [ ] 菜单经过可信目录规范化；`sub` 菜单已注册定义，`routePath`、`contextPath`、`activeRule` 的语义未混用
- [ ] `runtimePort.updateInstanceProps` 等待 `microApp.update()` 并入实例队列
- [ ] 挂载 props 来自 `buildPublicProps`；无 Token / 私钥字段
- [ ] `appKey === instanceId`；同时下发 `applicationCode` 与 `viewId`
- [ ] 切 Tab 不 unmount；关 Tab / 登出 / 授权上下文切换路径调用 `releaseInstance`；首次挂载失败完整回滚并可重试
- [ ] Locale / AuthInvalid 走 `notifyLocale` / `notifyAuthInvalid`（或等价且文档化的兼容双路径，并有退出计划）
- [ ] 消息处理器与文档承诺一致（无「假实现」REQUEST_TOKEN）
- [ ] 微应用深链经 Redirect Gateway、会话与菜单就绪后恢复；URL 未直接决定子应用 `entry`
- [ ] 敏感信息不进源码、Mock、URL、公开 props
- [ ] `npm run build`（壳仓）通过；协议变更有真实子应用联调计划（推荐 Member）
- [ ] 偏差写入壳仓 `docs/architecture/deviations.md`；公开契约变更同步 appkit `CHANGELOG` 与相关 docs

未执行的检查必须在交付说明中显式标出，不得默认为已通过。

---

## 11. 相关入口

- 中央 Profile：`g2rain` → `docs/architecture/profiles/frontend-shell/`（含 `shell-generation-policy.md`）
- Appkit 包实现：`packages/platform/src/main/index.ts`、`packages/http`
- [HTTP 与 Runtime 契约](https://github.com/g2rain/g2rain-appkit/blob/main/docs/packages/http-runtime-contract.md)
- [人工接线](https://github.com/g2rain/g2rain-appkit/blob/main/docs/integration/main.md)
- [AI Coding 提示词](https://github.com/g2rain/g2rain-appkit/blob/main/docs/integration/main-shell-generation-prompt.md)
- [Sub 侧对照](https://github.com/g2rain/g2rain-appkit/blob/main/docs/integration/app.md)
- [Appkit 安全边界](https://github.com/g2rain/g2rain-appkit/blob/main/docs/security/security-boundaries.md)
- 生成验证参考：`g2rain-admin-shell`（菜单注册、Workspace、RuntimeAdapter、认证桥接与深链恢复）
- 存量迁移参考：`g2rain-main-shell`（`docs/project.yaml`、`AGENTS.md`、`docs/architecture/*`）
