# 开始探索与开发

## 只想先了解

1. 阅读[平台概览](overview.md)，选择知识库、Agent 或业务应用方向。
2. 打开对应项目 README，确认环境要求、配置来源和当前验证状态。
3. 遇到不清楚的步骤，带上仓库名和目标，到 [Issues](https://github.com/g2rain/g2rain/issues/new/choose) 提问。

## 准备动手运行

- **知识库与 Agent**：先按[集成设计的交付顺序与验收门槛](architecture/knowledge-service-integration-design.md#交付顺序与验收门槛)了解依赖和验证缺口；具体运行步骤由责任项目维护，本仓库不提供未经验证的启动命令。
- **平台环境**：进入 [g2rain-deploy](https://github.com/g2rain/g2rain-deploy)，先阅读环境要求与初始化说明，再配置和启动。不要将默认配置视为可直接上线的配置。
- **开发新应用**：按[前端 CLI 登记](architecture/platform-tools/g2rain-app-cli.md)或[后端 Crafter 登记](architecture/platform-tools/g2rain-crafter.md)了解工具与模板的兼容关系，再进入对应仓库操作。

本仓库没有应用构建或启动命令。架构贡献者从 [AGENTS.md](../AGENTS.md)、[项目元数据](project.yaml)及[架构基线](architecture/README.md)开始。

## 文档与架构贡献

本仓库只维护平台资料和架构治理，提交前检查 Markdown 相对链接与锚点，并执行 `git diff --check`。具体应用的依赖安装、运行、构建和测试在责任项目中进行。

[贡献指南](../community/CONTRIBUTING.md) · [文档总目录](index.md)
