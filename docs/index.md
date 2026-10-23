# 谷雨G2rain智能体平台文档导航

这里连接组织介绍、平台专题、开发入口与架构治理。应用运行配置、部署步骤和测试记录由各项目维护，本目录提供平台级入口与协作规则。

## 按阅读目标开始

| 你想了解什么 | 推荐阅读 |
| --- | --- |
| 组织在做什么，为什么值得关注 | [组织首页](../README.md) → [平台概览](overview.md) |
| 企业权限怎样建模与校验 | [权限模型与完整说明图](permission-model.md) |
| 知识与 Agent 的界面和业务场景 | [三张界面展示](showcase.md) → [知识与 Agent 导览](overview.md#知识库) |
| 如何动手开发或部署 | [开始探索与开发](getting-started.md) |
| 哪些已登记，哪些还在验证或规划 | [项目进展与路线图](roadmap.md) → [项目架构目录](architecture/catalog/projects.yaml) |
| 与其他开源项目如何选型 | [RuoYi、Dify、RAGFlow 对比](comparison.md) |
| 如何参与 | [贡献指南](../community/CONTRIBUTING.md) → [社区与治理](#社区与治理) |

## 文档结构与维护分工

| 资料 | 用途与维护边界 |
| --- | --- |
| `README.md` | 组织定位、核心方向、项目导航和参与入口 |
| `docs/overview.md` | 平台概览与知识、Agent 的探索导览 |
| `docs/permission-model.md` | 权限领域对象、服务分工、依据与核对边界 |
| `docs/showcase.md`、`docs/images/showcase/` | 界面截图与可见交互说明 |
| `docs/getting-started.md` | 按目标选择责任项目与开发路径 |
| `docs/roadmap.md` | 进展、验证事项、规划与后续参与方向 |
| `docs/comparison.md` | 选型维度、范围和官方来源 |
| `docs/architecture/` | 正式架构规则、Profile、ADR、平台登记与项目目录 |
| `community/`、`governance/` | 贡献、行为准则、治理与安全披露 |

首页保持简明介绍，细节在对应专题中维护。项目运行事实由责任仓库提供；中央目录集中记录架构采用状态，专题文案不能替代或静默改变基线。

## 平台资料与执行入口

- [中央项目元数据](project.yaml)
- [AI Coding 执行入口](../AGENTS.md)
- [组织默认文档约定](organization-defaults.md)
- [知识服务集成设计与闭环评估](architecture/knowledge-service-integration-design.md)
- [目标架构与历史设计图](architecture.md)（包含规划流程，非交付清单）

## 架构治理

- [Java 领域服务 Profile](architecture/profiles/java-domain-service/README.md)
- [前端 App Profile](architecture/profiles/frontend-app/README.md)
- [前端 Shell Profile](architecture/profiles/frontend-shell/README.md)
- [Gateway Service Profile（Draft）](architecture/profiles/gateway-service/README.md)
- [平台唯一服务目录](architecture/platform-services/README.md)
- [g2rain-iam 平台服务登记](architecture/platform-services/g2rain-iam.md)
- [平台共享库目录](architecture/platform-libraries/README.md)
- [g2rain-appkit 平台共享库登记](architecture/platform-libraries/g2rain-appkit.md)
- [g2rain-crafter 平台工具登记](architecture/platform-tools/g2rain-crafter.md)
- [g2rain-generator-maven-plugin 平台工具登记](architecture/platform-tools/g2rain-generator-maven-plugin.md)
- [平台工具目录](architecture/platform-tools/README.md)
- [g2rain-app-cli 平台登记](architecture/platform-tools/g2rain-app-cli.md)
- [组织级架构决策](architecture/decisions/README.md)
- [项目架构目录](architecture/catalog/projects.yaml)
- [架构迁移流程](architecture/migrations/README.md)
- [架构治理机制](../governance/architecture-governance.md)

## 社区与治理

- [贡献指南](../community/CONTRIBUTING.md)
- [行为准则](../community/CODE_OF_CONDUCT.md)
- [组织治理](../governance/governance.md)
- [安全与漏洞披露](../governance/security.md)

## 事实来源边界

```text
g2rain/g2rain
  平台架构、Profile、跨项目 ADR、治理和项目目录

各项目仓库
  Requirements、领域 Design、项目 ADR、配置、部署和架构例外
```


## 维护者资料

[展示改造核对记录与待确认设置](showcase-review.md)用于维护 About、Topics、公开演示和联系方式等事项，不作为首次阅读的产品说明。
