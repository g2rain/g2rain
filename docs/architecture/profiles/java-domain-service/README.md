# Java 领域服务 Profile

版本：`1.0.0`　状态：正式

发布标识：`architecture-v1.0.0`　发布日期：`2026-08-24`

该 Profile 适用于拥有独立领域数据、以 Java/Spring Boot 运行、并通过 API/Biz/Startup 三模块交付的服务。`g2rain-member` 已正式采用该基线，`g2rain-department` 应在源码审核后接入同一基线。

## 强制规则

1. 模块依赖固定为 `startup → biz → api`。
2. API 发布可复用查询和少量具有明确业务语义的受信契约，不发布宽泛远程 CRUD。
3. Biz 拥有领域规则、事务、幂等、状态变化和数据访问。
4. Startup 只组装运行时，不承载领域逻辑。
5. 其他后端模块同步依赖以查询为主。
6. 数据编辑优先由 App 经 Gateway 调用数据所有者，或由数据所有者消费领域消息后自主完成。
7. 同步跨模块写入属于架构例外，必须有 Requirements、权限和事务设计，长期例外增加 ADR。
8. 所有租户数据必须保持可信上下文与持久化记录的租户一致性。
9. MySQL 最低版本为 `8.0.13`；仅有效记录唯一且逻辑删除后允许重建时，使用 `IF(delete_flag = 0, 0, NULL)` 函数唯一索引。
10. Agent 在任务期间动态检查架构和文档，不要求仓库维护 Agent 专用验证脚本。

## 专题规范

- [模块结构](modules.md)
- [DTO 与模型边界](dto-policy.md)
- [API 与模块协作](api-policy.md)
- [事务与并发](transaction-policy.md)
- [数据库与租户数据](database-policy.md)
- [安全边界](security-policy.md)
- [测试策略](testing-policy.md)
- [完成定义](definition-of-done.md)

## 项目允许自定义

- 领域职责与非职责。
- 数据库、外部基础设施和事件类型。
- 公开查询、App 写用例和受信内部契约。
- 领域状态机、错误码和项目测试矩阵。
- 经 `docs/architecture/deviations.md` 登记的架构例外。

## 不适用情况

公共 JAR、Spring Boot Starter、Maven 插件、Gateway、IAM 和前端项目不应直接采用此 Profile，应使用对应类型的独立基线。
