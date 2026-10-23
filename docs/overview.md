# 谷雨G2rain智能体平台概览

G2rain 面向企业应用的长期演进，探索将 Java 微服务、Vue 3 微前端、统一身份与访问、工程工具及 AI 能力组合为面向企业的开源智能体平台。组织以多仓库方式维护项目，各项目独立维护实现、配置、部署与验证记录。

## 平台核心价值

**企业级权限管控 + 智能体编排**：通过组织、应用授权、角色、控制域、控制单元与资源关系组织企业能力的交付和使用边界，并以知识接入与 Agent 编排探索业务流程的组合执行。权限对象及服务分工见[权限领域模型](permission-model.md)；具体集成状态以责任项目记录为准。

## 推荐探索路径

| 目标 | 入口 |
| --- | --- |
| 知识接入、检索与受控问答 | [知识库导览](#知识库) |
| Agent 知识证据调用与对话编排 | [Agent 导览](#agent-能力)；公开体验地址待补 |
| 开发前端或后端应用 | [前端 CLI](architecture/platform-tools/g2rain-app-cli.md) · [后端 Crafter](architecture/platform-tools/g2rain-crafter.md) |
| 了解平台规则与项目状态 | [架构基线](architecture/README.md) · [项目目录](architecture/catalog/projects.yaml) |
| 运行平台环境 | [部署仓库](https://github.com/g2rain/g2rain-deploy) |

## 知识库

从[知识服务集成设计](architecture/knowledge-service-integration-design.md)了解文件与 FAQ、知识版本、审核发布、检索证据与反馈修订的目标闭环。运营 App 经 Gateway 调用管理用例，知识服务拥有知识状态与活动版本，Agent Runtime 消费有业务语义的检索契约。

界面示例见[产品展示](showcase.md)，运行与验收信息见[项目进展](roadmap.md)。中央设计保留其评估日期，具体实现和配置由责任项目维护。

## Agent 能力

Agent 是平台能力方向。当前中央设计中的 Runtime 负责提问、对话编排、基于证据组织回答与人工接管决策；知识服务校验组织和知识数据归属。Agent 不直接读取知识服务数据库或向量索引。

面向 SaaS 场景，需要联合验证调用身份、租户上下文、接口权限、引用与故障行为。能力绑定与用户授权的边界见[权限模型](permission-model.md#与智能体编排的关系)，验收要求见[集成设计](architecture/knowledge-service-integration-design.md#交付顺序与验收门槛)。

## 能力与状态

- **架构与治理**：中央仓库维护版本化 Profile、跨项目 ADR、项目目录与迁移机制。
- **平台与研发基础**：已有 IAM、Shell、App 模板、生成工具与共享库的职责登记；项目采用、发布与集成验证状态各不相同。
- **知识库与 Agent**：当前中央[集成设计](architecture/knowledge-service-integration-design.md)描述知识生命周期、检索证据、Runtime 对话编排与跨仓库验收边界。截至设计稿标注的 2026-10-04，可执行业务和运行闭环尚未完成；不将目标设计描述为现有交付结果。
- **商业化与分析愿景**：计量、订阅、计费、账单、对账、结算、支付和 BI 属于规划方向；中央目录尚未将其登记为已交付的完整平台闭环。并发规模与生产就绪程度也需实际验证，不能由架构设计推定。

## 职责边界

本仓库是平台架构与治理的事实来源，不承载应用运行实现。各业务仓库维护 Requirements、领域 Design、配置、部署、测试和项目级例外。

继续阅读[平台架构说明](architecture.md)、[项目进展与路线图](roadmap.md)与[贡献指南](../community/CONTRIBUTING.md)。
