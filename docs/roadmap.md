# 项目进展与路线图

以下按现有资料归纳探索方向和验证缺口，未承诺发布日期。欢迎通过 Issue 提出优先级建议。

| 阶段 | 方向 | 事实来源或完成条件 |
| --- | --- | --- |
| 已建立并持续维护 | 架构 Profile、ADR、项目目录、平台工具与 IAM 契约 | [中央架构基线](architecture/README.md)；各项目采用与验证状态不同 |
| 验证与完善中 | Appkit 整体验证、Gateway 试点与安全差异关闭 | 对应项目验证记录及[项目目录](architecture/catalog/projects.yaml) |
| 待确认的展示工作 | 公开体验地址、操作视频与端到端操作指南 | 负责人确认公开地址、演示数据与实际验收范围后再补充 |
| 设计或规划 | 知识服务与 Agent 证据闭环、联邦转发与授权组件、计量及商业化流程 | [知识服务集成设计](architecture/knowledge-service-integration-design.md)、[ADR-0008](architecture/decisions/0008-federation-forwarder.md)及后续领域设计；不作为已交付能力宣传 |

## 如何理解当前进展

维护者已提供知识问答、知识版本管理和执行追踪的界面截图，见[产品展示](showcase.md)。截图可说明可见交互，不替代跨服务安全、恢复或生产环境验收。

[知识服务集成设计](architecture/knowledge-service-integration-design.md)保留截至 2026-10-04 的历史评估。本次组织首页重排没有重新运行该链路，也未据截图修改历史结论；后续应由责任项目同步最新验证证据。

[权限模型核对说明](permission-model.md#核对依据与状态差异)记录 Basis 与 Gateway 文档的更新差异。角色、租户与 Agent 调用权限是否在目标部署中贯通，仍需以真实集成为依据。

## 参与路线图

欢迎从文档与链接修正、上手反馈、知识问答案例、接口权限验证等具体任务参与。提出建议时说明目标项目、业务场景和可验证的完成条件，通过 [Issues](https://github.com/g2rain/g2rain/issues) 或 [Discussions](https://github.com/g2rain/g2rain/discussions)交流；尚未约定的负责人和发布日期不在此预设。
