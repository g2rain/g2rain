# g2rain 开源平台

**g2rain** 是一个面向企业级场景的开源平台，围绕认证与授权、微前端框架、统一管理后台等能力，提供一整套从网关、SSO、到前端壳应用和子应用的解决方案。

本仓库是 **g2rain GitHub Organization 的入口项目**，用于集中说明平台愿景、整体架构、治理与贡献方式，并统一指向各个子项目。

---

## 🌐 官方入口

- **官方网站**：`https://www.g2rain.com` 
- **文档站点**：`https://docs.g2rain.com`
- **GitHub 组织**：[`g2rain`](https://github.com/g2rain)  
- **讨论区 / Discussions**：  
  [`g2rain/g2rain`](https://github.com/g2rain/g2rain/discussions)

> 注：以上链接请根据实际情况替换为真实地址。

---

## 🧩 生态总览

g2rain 组织下推荐的核心仓库：

- **主应用 / 壳应用**
  - `g2rain-main-shell`：管理后台主应用（微前端壳），负责菜单、Tab、子应用装载、SSO 等。
- **子应用模板 & 脚手架**
  - `g2rain-app-template`：基于 Vue 3 + Vite + qiankun 的子应用模板（支持 SSO / DPoP / Token 管理）。
  - `g2rain-app-cli` (`create-g2rain-app`)：子应用脚手架，基于 `g2rain-app-template` 快速创建新项目。
- **IAM / 网关 / 后端相关**（示例名称，按实际仓库调整）
  - `g2rain-iam`：身份认证与授权服务（SSO、Token 签发与校验）。
  - `g2rain-gateway`：API 网关与路由转发。

完整列表请参见 [g2rain 组织主页](https://github.com/g2rain)。

---

## 🏗 架构与设计

更详细的架构说明请参考：

- [`docs/overview.md`](docs/overview.md)：平台整体概览
- [`docs/architecture.md`](docs/architecture.md)：核心组件、交互流程、技术选型

如果你只想快速上手前端部分，可以优先查看：

- `g2rain-main-shell` 的 `README.md`
- `g2rain-app-template` 的 `README.md`
- `g2rain-app-cli` 的 `README.md`

---

## 👥 社区与治理

g2rain 遵循开放、透明、可持续的治理原则。

- **治理模型**：见 [`governance/governance.md`](governance/governance.md)
- **角色说明**：Maintainer / Committer / Contributor 角色与权限说明见  
  [`governance/roles.md`](governance/roles.md)
- **安全与披露流程**：见 [`governance/security.md`](governance/security.md)

如果你希望更深度参与，可以：

- 参与 GitHub Discussions 讨论新特性与路线图
- 参与社区例会：会议纪要记录于 `community/meeting-notes/`

---

## 🤝 如何贡献

我们非常欢迎你参与 g2rain 的建设！

1. 阅读 [`community/CONTRIBUTING.md`](community/CONTRIBUTING.md) 获取详细贡献指南：
   - 如何提交 Issue / Bug 报告
   - 如何提 Pull Request
   - 代码风格与 Commit 规范（如使用 Conventional Commits）
2. 阅读 [`community/CODE_OF_CONDUCT.md`](community/CODE_OF_CONDUCT.md) 了解社区行为准则。
3. 在相关子项目中创建 Issue 或 PR，例如：
   - UI / 前端功能问题：`g2rain-main-shell`、`g2rain-app-template`
   - 脚手架与模板生成：`g2rain-app-cli`
   - SSO / 网关能力：`g2rain-iam`、`g2rain-gateway`（示例）

---

## 💬 讨论与支持

- **GitHub Discussions**：建议用来讨论新特性、设计方案与问答
- **Issues**：建议仅用于 Bug 报告与明确的功能需求
- **邮件列表 / IM 群组**（如果有）：可在 [`community/`](community/) 目录中补充相关信息

你可以在本仓库或任意子项目中发起 Discussions，我们会尽量统一在组织层面进行跟进。

---

## 📜 License

除非单个仓库另有声明，否则 g2rain 相关代码默认遵循以下开源协议：

- **License**：参见本仓库的 [`LICENSE`](LICENSE) 文件  

---

## 🧭 下一步你可以做什么？

- 想要 **快速体验**：从 `g2rain-main-shell` 和 `g2rain-app-template` 开始。
- 想要 **搭建自己的子应用**：使用 `g2rain-app-cli` 创建项目。
- 想要 **了解整体方案**：阅读本仓库 `docs/` 中的架构与路线图文档。
- 想要 **参与贡献**：从简单的 Issue、文档修正或示例补充开始。

欢迎加入 g2rain 社区，一起打造更好用的企业级开源平台！