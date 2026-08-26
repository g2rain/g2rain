# 模块结构

## 标准模块

```text
<service>-startup
        │ depends on
        ▼
<service>-biz
        │ depends on
        ▼
<service>-api
```

| 模块 | 职责 | 禁止事项 |
| --- | --- | --- |
| API | 查询条件、VO、枚举、可复用接口和明确的受信契约 | 依赖 Biz/Startup，发布 PO、Service 或通用远程 CRUD |
| Biz | Controller、Service、Domain、DAO、Converter、本地写入 DTO | 依赖 Startup，把事务和领域规则放入 Controller |
| Startup | 启动类、运行配置、Actuator、镜像组装 | 供下层反向依赖，承载业务规则 |

根 POM 统一声明模块、版本和构建插件。新增、拆分或合并模块必须同步修改项目元数据、架构文档；影响多个项目的变更先修改中央 Profile 和 ADR。

## 包级依赖

```text
Controller → Service → DAO
                 └──→ Domain / Client
```

- Controller 负责协议、参数绑定、校验入口和响应包装。
- Service 负责业务用例、事务、幂等、状态、安全和跨 DAO 协调。
- DAO 只负责持久化。
- Domain 优先承载无框架依赖的纯规则。
- Converter 不发起数据库或网络调用。

