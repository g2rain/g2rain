# 谷雨 G2rain 前端工具链 v1.0.1｜从创建工程到业务开发

**一个命令创建业务应用或主壳，用一套公共组件与平台能力，让更多时间留给业务开发。**

谷雨 G2rain 是面向企业的开源智能体平台。本次集中介绍 **Appkit、App CLI、App Template、Shell Template v1.0.1**：从公共能力、工程创建到主子应用模板，为 Vue 3 业务应用提供配套开发工具。

**[访问官网](https://www.g2rain.com) · [探索源码](https://github.com/g2rain/g2rain) · [开发入门](https://github.com/g2rain/g2rain/blob/main/docs/getting-started.md)**

## 本次重点：把更多时间留给业务开发

开发一个业务应用，除了页面，还需要处理请求、登录会话、权限、语言、主题和主子应用协作。此次发布的四个项目，将这些重复工作组织成可复用的开发基础。

| 项目 | 为你解决什么 | 发布入口 |
| --- | --- | --- |
| **Appkit** | 将主题、UI 组件、HTTP 与应用运行能力沉淀为四个公共包，减少重复建设 | [v1.0.1](https://github.com/g2rain/g2rain-appkit/releases/tag/v1.0.1) |
| **App CLI** | 创建业务应用与主壳，从 SQL 生成 CRUD 骨架，从页面声明提取权限资源 | [v1.0.1](https://github.com/g2rain/g2rain-app-cli/releases/tag/v1.0.1) |
| **App Template** | 提供 Vue 3 业务子应用起点，支持独立开发、微前端集成、认证与动态资源路由 | [v1.0.1](https://github.com/g2rain/g2rain-app-template/releases/tag/v1.0.1) |
| **Shell Template** | 提供菜单、Tab 与工作区主入口，组织业务子应用的挂载和协作 | [v1.0.1](https://github.com/g2rain/g2rain-shell-template/releases/tag/v1.0.1) |

### Appkit：常用组件与平台能力，按需使用

- `@g2rain/theme`：统一设计变量、亮暗主题和 Element Plus 样式映射。
- `@g2rain/ui`：查询表单、远程选择、多列排序、组织与字典组件、状态开关。
- `@g2rain/http`：统一请求与错误处理，支持 Token 刷新、可选 DPoP 和 SSE 流式响应。
- `@g2rain/platform`：主子应用协作、实例生命周期，以及权限、国际化、主题、Loading 和消息能力。

v1.0.1 进一步完善并发 Token 刷新、SSE 取消与连接释放、选择器请求、状态开关权限、多列排序和主题同步等细节，让这些日常交互更可靠。

### App CLI + 两个模板：从创建工程到开发页面

先用 CLI 选择业务 App 或主壳，再在模板基础上开发。业务子应用还可以从本地 SQL 生成页面、API、类型和 Mock 骨架，并从页面中的静态权限声明生成资源配置。

**创建工程 → 开发业务页面 → 生成权限资源 → 按平台流程接入与授权。**

本轮两个模板统一使用 Appkit 1.0.1；CLI 同步对应模板快照，并完善参数校验、项目身份替换和权限扫描。App 模板改进登录恢复与国际化，Shell 模板改进深链跳转、页面缓存和子应用生命周期管理。

## 现在就创建你的第一个应用

准备 Node.js **22 或以上版本**与 npm，在新项目的父目录执行：

```bash
npx create-g2rain-app@1.0.1 app g2rain-demo-app --context-path demo
cd g2rain-demo-app
npm install
npm run dev
```

需要创建主入口时，在另一个项目目录执行：

```bash
npx create-g2rain-app@1.0.1 shell g2rain-demo-shell --context-path admin --port 3000
cd g2rain-demo-shell
npm install
npm run dev
```

生成后按项目 README 配置运行模式、认证、网关与服务地址；业务 App 的独立开发可使用 `?mode=alone`。主壳与子应用的实际接入需要配置对应应用入口。

已有 Vue 项目也可从 [Appkit](https://github.com/g2rain/g2rain-appkit) 按需接入公共能力。想了解代码生成和权限资源提取，可继续阅读 [App CLI 使用说明](https://github.com/g2rain/g2rain-app-cli/tree/v1.0.1#readme)。

## 一起把真实业务场景做出来

如果你正在建设业务管理系统、拆分微前端应用，或希望统一团队的组件与工程规范，欢迎从创建第一个业务 App 开始使用 G2rain。

- **[Star 主仓库](https://github.com/g2rain/g2rain)**，收藏平台与项目入口。
- **[关注 G2rain](https://github.com/g2rain)**，了解后续进展。
- **[分享需求与实践](https://github.com/g2rain/g2rain/discussions)**，交流你的业务应用与前端开发场景。
- **[反馈使用问题](https://github.com/g2rain/g2rain/issues)**，帮助完善上手体验。

本次为主仓库的前端工具链发布介绍，四个项目均已有独立的 v1.0.1 Release。生成工程后，仍需按对应项目文档配置认证、网关和应用入口；页面权限声明需配合平台授权与服务端鉴权使用。
