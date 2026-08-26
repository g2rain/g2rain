# ADR-0001：Java 领域服务采用 API/Biz/Startup 三模块

## 状态

已接受（纳入 `java-domain-service 1.0.0`）

## 背景

多个 Java 领域服务需要对外发布稳定契约、实现领域逻辑并组装独立运行时。如果每个仓库自行划分模块，依赖方向和发布边界会逐渐分叉。

## 决策

同类领域服务统一采用：

```text
startup → biz → api
```

API 发布稳定契约；Biz 实现领域规则和持久化；Startup 负责 Spring Boot 组装。禁止反向依赖。

## 后果

- Member、Department 等同类项目采用相同模块骨架。
- 新模块或例外必须更新项目元数据；跨项目变化先修改 Profile。
- 不适合三模块结构的 IAM、Gateway、公共库和前端项目使用其他 Profile。
