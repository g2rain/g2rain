# 组织级架构决策

本目录记录影响多个 g2rain 仓库的长期架构决定。只影响单个领域的决定保留在对应项目 `docs/decisions`。

## 当前决策

- [ADR-0001：Java 领域服务采用 API/Biz/Startup 三模块](0001-java-domain-service-modules.md)
- [ADR-0002：模块间同步协作以查询为主](0002-query-first-module-collaboration.md)
- [ADR-0003：写入由 App 或领域消息驱动](0003-app-and-event-driven-writes.md)
- [ADR-0004：逻辑删除唯一约束使用函数索引](0004-logical-delete-unique-indexes.md)
- [ADR-0005：前端 App 采用分层单向依赖](0005-frontend-app-layering.md)
- [ADR-0006：前端 App 支持集成与独立双运行模式](0006-frontend-app-runtime-modes.md)

## 状态

`建议`、`已接受`、`已废弃`、`已替代`。ADR 不因过时直接删除；新决策替代旧决策时保留历史和引用关系。
