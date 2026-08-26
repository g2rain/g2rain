# g2rain 平台文档

本目录维护 g2rain 的平台级架构、项目架构基线和跨仓库治理规则。各业务仓库继续维护自身需求、领域设计、配置、运维和项目级架构决策。

## 平台资料

- [平台架构说明](architecture.md)
- [平台概览](overview.md)
- [组织级架构基线](architecture/README.md)

## 架构治理

- [Java 领域服务 Profile](architecture/profiles/java-domain-service/README.md)
- [前端 App Profile](architecture/profiles/frontend-app/README.md)
- [前端 Shell Profile](architecture/profiles/frontend-shell/README.md)
- [平台唯一服务目录](architecture/platform-services/README.md)
- [g2rain-iam 平台服务登记](architecture/platform-services/g2rain-iam.md)
- [平台工具目录](architecture/platform-tools/README.md)
- [g2rain-app-cli 平台登记](architecture/platform-tools/g2rain-app-cli.md)
- [组织级架构决策](architecture/decisions/README.md)
- [项目架构目录](architecture/catalog/projects.yaml)
- [架构迁移流程](architecture/migrations/README.md)
- [架构治理机制](../governance/architecture-governance.md)

## 事实来源边界

```text
g2rain/g2rain
  平台架构、Profile、跨项目 ADR、治理和项目目录

各项目仓库
  Requirements、领域 Design、项目 ADR、配置、部署和架构例外
```
