# Shell 生成与需求输入规范

状态：`Draft`

本规范用于由 AI 助手创建、改造和检查 Vue 微前端主应用 Shell。它补充 [frontend-shell Profile](README.md) 与 [Main Shell 契约](main-shell-contract.md)，不替代 IAM、Gateway 或子应用项目的事实来源。

`g2rain-app-cli` 现支持两个项目族：`create-g2rain-app app`（或默认）生成 `frontend-app`；`create-g2rain-app shell` 基于独立模板仓 `g2rain-shell-template` 生成 `frontend-shell` 基线。本规范不得被解释为允许用业务子应用模板（`g2rain-app-template` / `frontend-app`）改造成 Shell。深度定制、真实 SSO、业务菜单与子应用联调仍须按下方需求规格补齐，不能把脚手架成功表述为联调验收通过。

## 1. 生成目标与边界

生成结果是平台入口和编排者，负责全局布局、导航、工作区、子应用定义、生命周期、浏览器会话协调及部署配置。它不得生成或拥有：

- 子应用内部业务页面、领域 API、领域 Store 或业务 Mock；
- IAM Token 签发、Gateway/后端的最终鉴权与数据授权；
- 未经验证的任意远程微应用入口、生产 Secret 或私钥；
- 把子应用实现复制进主应用的“示例业务”。

生成的基础分层继承 `frontend-app`：`shared → components → platform → runtime → views/shell`。`shell` 只承载布局与编排；微应用注册、实例队列和运行适配器保持 Shell 所有，不能下沉到业务 views 或子应用。

## 2. 必填需求输入

每次 AI 创建或改造都必须获得一份符合 [Shell 需求规格模板](shell-requirement-template.md) 的规格，至少包含以下字段：

| 输入域 | 必填内容 | 不得自动猜测的事项 |
| --- | --- | --- |
| 项目身份 | `projectName`、`applicationCode`、`contextPath`、仓库信息 | 生产域名、组织或租户标识 |
| Shell 形态 | 单工作区/多 Tab、是否允许同一子应用多实例、默认首页 | Tab 保活与关闭策略 |
| 子应用注册 | 每个应用的 `appKey`、`name`、`entry`、`activeRule`、可信来源 | 远程 entry、跨域白名单、应用权限 |
| 会话与安全 | IAM/Gateway 接入方式、Token 最小传递范围、消息校验策略 | Secret、私钥、生产回调 URI |
| 路由与导航 | 菜单来源、Shell 路由、深链和 404 策略 | 路由冲突解决与权限降级行为 |
| 运行与部署 | dev port、容器端口、静态资源路径、代理/运行时配置来源 | 生产环境变量值、密钥注入方式 |
| 验收 | 至少一个真实子应用、构建和浏览器联调范围、回滚方案 | 未执行验证的“通过”结论 |

缺少会影响认证、远程入口、部署路径、实例隔离或路由语义的输入时，AI 必须停止并请求明确决策；不得用示例值、隐式公网地址或宽松安全默认值补齐。

## 3. 生成输出契约

AI 创建的新 Shell 至少输出：

```text
AGENTS.md
docs/project.yaml
docs/index.md
docs/architecture/{overview,layers,dependencies,runtime-flows,deviations}.md
docs/development/{local-development,testing,definition-of-done}.md
docs/operations/{configuration,deployment,troubleshooting}.md
docs/security/security-boundaries.md
docs/requirements/README.md
src/{shared,components,platform,runtime,views,shell}/
```

输出必须：

1. 固定 `frontend-app` 与 `frontend-shell` Profile 版本及中央架构快照。
2. 在 `docs/project.yaml` 记录生成器版本、模板 Ref、应用编码、Context Path、端口与子应用契约字段。
3. 提供类型化的子应用定义、实例标识、生命周期适配边界、消息信封和卸载清理点；不能把 qiankun Props 或 Token 写入全局无类型对象。
4. 默认不注册真实生产子应用；示例注册必须使用显式开发占位值，并在构建、运行和文档中清晰标记。
5. 不写入 Secret、Token、私钥或生产凭据；公开运行时配置只保存非敏感定位信息。

## 4. AI 生成流程

```text
读取 Shell 需求规格与当前项目事实
→ 校验必填输入、安全决策及适用的 Profile 版本
→ 创建或改造项目身份、文档和最小 Shell 骨架
→ 静态校验契约、路径和残留占位符
→ 安装依赖/执行构建（获得授权时）
→ 使用至少一个真实子应用完成浏览器联调
→ 回写验证结果与已知偏差
```

AI 可以根据需求生成布局、导航、应用注册接口和运行适配器，但不能把“生成成功”表述为运行验证成功。任何对消息、Token、`appKey`、`name`、`entry`、`activeRule` 或 `instanceId` 的变更，都必须同步影响分析、兼容策略和回滚说明。

## 5. 验收基线

创建后的最小验收包括：

- `npm run build` 通过，文档链接与 `docs/project.yaml` 一致；
- 子应用定义唯一性、可信 entry 和 `activeRule`/Context Path 兼容性可校验；
- Shell 路由、菜单、Tab 和实例状态各自存在单一事实来源；
- 生命周期覆盖 bootstrap、mount、update、unmount、destroy 的失败和清理路径；
- 至少一个真实子应用完成冷启动、首次挂载、内部路由、Token/Locale 更新、关闭卸载和深链刷新验证；
- 发现未完成的 Main/Sub 协作、安全或部署事项时，写入项目 `docs/architecture/deviations.md`，不通过临时代码绕过。

## 6. AI 审核要求

AI 对已生成或已有 Shell 执行检查时，必须：

1. 读取本需求规格、项目 `docs/project.yaml`、中央 `frontend-app`/`frontend-shell` Profile、项目偏差和 Git Diff。
2. 核对 `appKey`、`name`、`entry`、`activeRule`、`instanceId` 的定义、唯一性、可信来源与兼容策略。
3. 检查 Shell 的单一事实来源、生命周期清理、消息来源/目标校验、Token 最小传递及公开配置边界。
4. 检查 Context Path、Vite base、静态资源、Nginx/OpenResty、认证回调和子应用入口是否构成一致的部署契约。
5. 执行已声明的构建和适用测试；协议、路由、认证或部署变化必须至少使用一个真实子应用完成浏览器联调。未验证项应报告为风险，不得通过文档或生成代码掩盖。
