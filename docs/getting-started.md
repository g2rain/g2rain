# 开始探索与开发

## 在线体验

- [知识对话演示](https://demo.g2rain.com/chat/?entry=ek_xV3IEOTK8HfexoR3PupYcF)：体验知识对话入口，无需登录。
- [主应用演示](https://demo.g2rain.com/main/)：体验平台主应用入口，用户名和密码均为 `admin`。

接入交流请[联系维护者](https://www.g2rain.com/platform/contact.html)。

智能体平台与智能客服核心模块均已发布并开源；按项目文档部署接入，也可与维护者交流。

## 只想先了解

1. 阅读[平台概览](overview.md)，选择知识库、Agent 或业务应用方向。
2. 打开对应项目 README，确认环境要求、配置来源和当前验证状态。
3. 遇到不清楚的步骤，带上仓库名和目标，到 [Issues](https://github.com/g2rain/g2rain/issues/new/choose) 提问。

## 选择依赖版本

先查看[版本与依赖](versions.md)，选择已核实的 Maven / npm 制品和配套模板版本，再按项目文档配置与运行。

## 准备动手运行

平台已具备私有部署场景的可用性，可按目标选择已发布组件和部署扩展，在企业自己的环境搭建平台。云端运营所需的交易与计量计费不是私有部署的前置条件。

平台部署工具包含两个职责互补的入口：

- **快速启动与生产部署**：进入 [g2rain-bootstrap](https://github.com/g2rain/g2rain-bootstrap)，按目标组合基础平台、Agent 与 Knowledge 能力并生成部署方案。仓库默认配置是面向单请求、最低资源跑通的验证预设，不是生产容量建议；生产环境应按并发、数据量、模型调用与文档处理负载重新设置资源、凭据、TLS、端口、持久化、备份和恢复策略。
- **开发部署与联调测试**：进入 [g2rain-deploy](https://github.com/g2rain/g2rain-deploy)，使用开发阶段的 Compose 编排、初始化、启停、源码镜像构建、更新和业务扩展，完成跨模块联调测试。该仓库不是生产部署入口。

- **知识库**：从[知识服务 v1.0.0](https://github.com/g2rain/g2rain-knowledge/releases/tag/v1.0.0)和[运营端 v1.0.0](https://github.com/g2rain/g2rain-knowledge-app/releases/tag/v1.0.0)了解能力与配置，再按部署仓库的知识库扩展搭建。
- **智能体应用**：按部署仓库的 Agent 扩展与责任项目文档配置模型、知识和工具；跨服务场景参考[集成设计](architecture/knowledge-integration.md)。
- **平台环境**：两个仓库同属部署工具；快速启动和生产部署使用 `g2rain-bootstrap`，开发阶段部署与联调测试使用 `g2rain-deploy`。
- **开发新应用**：按[前端 CLI 登记](architecture/platform-tools/g2rain-app-cli.md)或[后端 Crafter 登记](architecture/platform-tools/g2rain-crafter.md)了解工具与模板的兼容关系，再进入对应仓库操作。

本仓库没有应用构建或启动命令。架构贡献者从 [AGENTS.md](../AGENTS.md)、[项目元数据](project.yaml)及[架构基线](architecture/README.md)开始。

## 文档与架构贡献

本仓库只维护平台资料和架构治理，提交前检查 Markdown 相对链接与锚点，并执行 `git diff --check`。具体应用的依赖安装、运行、构建和测试在责任项目中进行。

[贡献指南](../community/CONTRIBUTING.md) · [文档总目录](index.md)
