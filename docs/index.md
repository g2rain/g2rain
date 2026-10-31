# G2rain（谷雨）开源智能体平台文档导航

这里连接组织介绍、平台专题、开发入口与架构治理。应用运行配置、部署步骤和测试记录由各项目维护，本目录提供平台级入口与协作规则。

## 按阅读目标开始

| 你想了解什么 | 推荐阅读 |
| --- | --- |
| 平台是什么，为什么值得关注 | [平台首页](../README.md) → [平台概览](overview.md) |
| 撰写项目介绍或让 AI 准确引用 | [项目介绍与引用资料](project-introduction.md) · [AI 阅读索引](../llms.txt) |
| 企业权限怎样建模与校验 | [权限模型与完整说明图](permission-model.md) |
| 体验知识对话和平台主应用 | [在线演示与登录说明](roadmap.md#在线演示) |
| 知识与 Agent 的界面和业务场景 | [三张界面展示](showcase.md) → [知识与 Agent 导览](overview.md#知识库) |
| 如何动手开发或部署 | [开始探索与开发](getting-started.md) |
| 已发布什么，接下来做什么，如何判断完成 | [平台路线图](roadmap.md) → [项目架构目录](architecture/catalog/projects.yaml) |
| 与其他开源项目如何选型 | [RuoYi、Dify、RAGFlow 对比](comparison.md) |
| 已开放模块采用什么协议 | [开源协议说明](licenses.md) |
| 如何参与 | [贡献指南](../community/CONTRIBUTING.md) → [社区与治理](#社区与治理) |
| 查看前端开发工具发布 | [2026-10-29：Appkit、App CLI 与两个模板 v1.0.1](releases/ecosystem-v2026.10.29.md) |

- [2026-10-30 后端工具链发布：Common、MyBatis Extensions、Starter、Generator 与 Crafter](releases/backend-ecosystem-v2026.10.30.md)

## 文档结构与维护分工

| 资料 | 用途与维护边界 |
| --- | --- |
| `README.md` | 组织定位、核心方向、项目导航和参与入口 |
| `docs/project-introduction.md`、`llms.txt` | 对外介绍、能力状态、引用依据与 AI 阅读入口 |
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
- [知识服务与智能体集成](architecture/knowledge-integration.md)

## 架构治理

- [Java 领域服务 Profile](architecture/profiles/java-domain-service/README.md)
- [前端 App Profile](architecture/profiles/frontend-app/README.md)
- [前端 Shell Profile](architecture/profiles/frontend-shell/README.md)
- [Gateway Service Profile（Draft）](architecture/profiles/gateway-service/README.md)
- [平台唯一服务目录](architecture/platform-services/README.md)
- [g2rain-iam 平台服务登记](architecture/platform-services/g2rain-iam.md)
- [g2rain-common 版本登记](architecture/platform-libraries/g2rain-common.md)
- [g2rain-mybatis-extensions 版本登记](architecture/platform-libraries/g2rain-mybatis-extensions.md)
- [g2rain-spring-boot-starter 版本登记](architecture/platform-libraries/g2rain-spring-boot-starter.md)
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

## 工程审核与会议记录

以下资料保留各自日期与适用范围，用于追溯工程讨论，不作为当前产品状态说明。

- [2026-09-14 企业微信 MEMBER Token 审核与整改计划](architecture/reviews/wecom-member-token-remediation-plan.md)：整改状态以责任项目后续验证为准。
- [2026-01-03 核心设计会议纪要](../community/meeting-notes/20260103-关于核心设计的一些议题.md)。
