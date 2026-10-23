# G2rain 与 RuoYi、Dify、RAGFlow 的选型对比

资料核对日期：2026-10-19。本文用于解释建设目标和治理对象的区别，未进行同环境部署、性能测试、安全测评或采购评估。以下选型判断是基于官方资料与 G2rain 仓内事实的分析。

## 比较范围

- **RuoYi**：仅比较官方 `yangzongzhuan/RuoYi-Vue`；不代表 RuoYi-Cloud、RuoYi-Vue-Plus、ruoyi-vue-pro 等其他项目或分支。
- **Dify**：参考 `langgenius/dify` 官方 README 与账户模型，不把云服务、社区自托管与企业版本能力混为一谈。
- **RAGFlow**：指 `infiniflow/ragflow`。权限部分分别标明稳定文档与 `v1.0.0-rc1` 文档，后者不能直接视为所有已部署版本的能力。
- **G2rain**：参考[权限领域模型](permission-model.md)、[知识服务集成设计](architecture/knowledge-service-integration-design.md)和[项目目录](architecture/catalog/projects.yaml)。架构要求、界面演示与端到端验收分别判断。

## 核心维度

| 维度 | RuoYi-Vue | Dify | RAGFlow | 谷雨G2rain智能体平台 |
| --- | --- | --- | --- | --- |
| 建设起点 | Java 管理后台与业务快速开发 | LLM 应用、工作流、Agent 与 RAG 管线 | 文档知识处理、检索与 Agent | 企业权限治理与 智能体编排的平台组合 |
| 权限对象 | 用户、部门、角色、菜单及数据范围 | 工作空间成员角色；业务系统自身权限仍需对接评估 | 团队成员与知识库等资源共享；细粒度规则需按版本确认 | 组织、应用授权、控制域、控制单元、角色、页面与 API 资源 |
| SaaS 业务能力交付 | 本次资料主要描述后台权限，不据此推断所有衍生版的租户能力 | 需要区分 AI 工作空间治理与客户业务系统的应用开通、岗位权限 | 需要区分知识资源协作与客户业务系统的业务授权 | 显式建模机构应用开通、控制域与角色控制单元关联；不等同于完整订阅计费闭环 |
| 知识与 Agent | 本次官方 README 未将 AI 工作流与 RAG 列为主要能力；不据此断言扩展不可能 | 官方将 AI 工作流、RAG、Agent、模型管理与可观测性列为核心能力 | 官方将 RAG 与 Agent 结合；Agent 文档描述图式业务工作流编排 | 有知识服务跨仓库设计与维护者提供的界面演示；真实集成和授权传递需验证 |
| 更适合先验证什么 | 后台开发效率、部门数据范围与业务定制 | AI 流程构建、模型与工具集成体验 | 文档解析、检索质量、知识问答与 Agent 流程 | 租户开通、员工岗位授权、接口校验与 Agent 场景的共同适配 |

## G2rain 应突出什么

**面向需要长期建设业务平台的企业团队，把权限、应用交付和 Agent 能力放在同一架构语境中演进。**

具体可展示三类问题：

1. 某机构开通了哪些应用和能力组合？由应用授权、控制域与控制单元表达。
2. 机构内哪些岗位可以使用哪些功能？由用户、角色、控制单元和资源关联表达，再由 Gateway 与业务服务执行校验。
3. 知识和 Agent 如何参与业务执行？展示问答与追踪界面，同时验证真实调用身份、租户上下文和业务对象授权。

前两类模型已有文档与源码核对依据；第三类不能只靠界面或 Skill 绑定推定用户授权已贯穿全部工具。生产就绪、易用性或效果优于其他项目的结论，都需要场景一致的实测支持。

## 官方来源

- [RuoYi-Vue 官方 README](https://github.com/yangzongzhuan/RuoYi-Vue)：后台框架定位、用户与角色菜单权限、部门数据范围及代码生成能力。
- [RuoYi 官方后台手册：数据权限](https://doc.ruoyi.vip/ruoyi/document/htsc.html)：数据范围、角色与 DataScope 机制。
- [Dify 官方 README](https://github.com/langgenius/dify)：AI 工作流、RAG、Agent、模型管理与可观测性等能力。
- [Dify 官方账户模型](https://github.com/langgenius/dify/blob/main/api/models/account.py)：工作空间成员角色；具体权限须结合对应版本实现核对。
- [RAGFlow 官方 README](https://github.com/infiniflow/ragflow)：RAG 与 Agent 产品定位。
- [RAGFlow Agent 官方文档](https://ragflow.net/docs/category/agents)：可视化工作流与图式任务编排。
- [RAGFlow HTTP API](https://ragflow.io/docs/http_api_reference)：知识库的 `me` / `team` 权限字段。
- [RAGFlow 权限概览（v1.0.0-rc1）](https://ragflow.io/docs/v1.0.0-rc1/permission_system_overview)：团队成员、资源共享和操作权限的分层说明，属于指定预发布文档范围。

比较不采用“只有 G2rain 有企业权限”“Dify 没有权限”“RAGFlow 只能做检索”等绝对说法。没有在本次范围中核实到的能力，不直接标为不支持；也不以 Star 数作为业务适配性或安全性的证据。
