# 前端 App Profile

版本：`1.0.0`　状态：正式

该 Profile 适用于 g2rain Vue 3 业务应用、qiankun 子应用及其官方工程模板。`g2rain-app-template` 和 `g2rain-manager-app` 已完成首轮源码与生产构建验证；`g2rain-manager-app` 已建立项目级文档并显式采用本正式基线，模板项目仍按迁移流程升级。

Java 后端服务、公共 JAR、Spring Boot Starter、Gateway、IAM 和纯 Node CLI 不适用本 Profile。`g2rain-app-cli` 是 supporting tool，不采用 App 运行时分层，但必须遵守本 Profile 的[项目脚手架规范](scaffolding-policy.md)；其唯一官方脚手架身份和跨仓库关系见[平台工具登记](../../platform-tools/g2rain-app-cli.md)。

## 强制规则

1. 目标层次为 `shared → components → platform → runtime → views`，依赖只从上层指向下层；`main.ts`/`App.vue` 是组合根。
2. shared 不依赖 Vue 业务状态；components 不直接依赖 platform/runtime/views；platform 不依赖应用 runtime/views。
3. views 拥有业务页面、页面 API、业务类型和 Mock，不能把领域逻辑下沉到通用组件。
4. 可复用模块通过稳定 `index.ts` 暴露公共 API，不深度导入内部实现。
5. 微前端 App 同时设计集成模式和可诊断的独立模式；集成模式由 main-shell 传递可信运行上下文。
6. App 通过 IAM 完成认证，通过 Gateway 使用后端业务接口；前端权限不替代后端鉴权。
7. 页面代码和资源配置生成结果必须人工 Review、构建和测试，生成器不是架构或领域事实来源。
8. Token、私钥和生产 Secret 不进入前端 Bundle、运行时公开配置、Mock 或仓库。
9. 目录、公共 API、配置、生成器和运行流程变化必须同步项目 docs。
10. Agent 在任务期间动态检查架构、文档与 Diff，不要求仓库维护 Agent 专用验证脚本。

## 专题规范

- [层次与依赖](layers.md)
- [运行模式与平台协作](runtime-policy.md)
- [Views 与业务页面](views-policy.md)
- [Components 与 Platform](components-platform-policy.md)
- [代码生成](generation-policy.md)
- [项目脚手架与模板契约](scaffolding-policy.md)
- [资源配置生成](resource-policy.md)
- [安全边界](security-policy.md)
- [测试策略](testing-policy.md)
- [完成定义](definition-of-done.md)

## 项目允许自定义

- 具体业务页面、API、DTO 和页面状态。
- 应用编码、Context Path、资源编码和国际化 Tag。
- 主应用传递的扩展上下文，但必须保持安全和兼容边界。
- 项目专属组件、运行时适配和部署拓扑。
- 经 `docs/architecture/deviations.md` 登记的当前偏差与迁移计划。

## 正式版本

- Profile 版本：`1.0.0`
- 发布日期：`2026-08-25`
- 中央固定快照：`architecture-v1.1.0`
- 验证对象：`g2rain-app-template`、`g2rain-manager-app`

采用项目必须在 `docs/project.yaml` 固定 Profile 版本和中央快照，维护本地偏差并执行实际构建。Profile 正式发布不等于项目自动升级；项目需要审核当前源码、双运行模式、生成流程、部署配置和偏差后显式采用。
