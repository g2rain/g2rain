# g2rain-mybatis-extensions 平台共享库登记

## 登记信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [g2rain/g2rain-mybatis-extensions](https://github.com/g2rain/g2rain-mybatis-extensions) |
| 平台身份 | Supporting Library |
| 实现类型 | Maven 多模块公共库 |
| 当前版本 | `1.0.5` |
| 发布 Tag | [v1.0.5](https://github.com/g2rain/g2rain-mybatis-extensions/releases/tag/v1.0.5) |
| 发布核对日期 | 2026-10-30 |
| 项目文档 | [版本文档](https://github.com/g2rain/g2rain-mybatis-extensions/tree/v1.0.5/docs) |

## 能力与边界

MyBatis SQL 插件链、SQL 解析缓存、分页与 count，以及分页 Starter。

数据访问扩展按需消费；租户与数据授权策略由业务服务和接入层配置。

中央仓库维护定位、发布版本与跨仓库关系；实现、配置、测试和升级细节由责任仓库维护。本次按 GitHub Release 和同名 Tag 登记，不据此修改消费项目采用的架构 Profile，也不推定 Maven Central 制品可用性或环境联调结果。

## 本轮更新

### 核心依赖升级
- Spring Boot：4.0.5 → 4.1.2。
- MyBatis：3.5.19 → 3.6.0。
- MyBatis Spring Boot Starter：4.0.1 → 4.1.0，版本统一由属性管理。
- JDK 编译基线保持为 25。

### 其他依赖更新
- Caffeine：3.2.3 → 3.3.1。
- Kryo：5.6.2 → 5.7.1。
- Lombok：1.18.44 → 1.18.48。
- JUnit Jupiter：5.10.2 → 6.1.3。
- Mockito：5.23.0 → 5.24.0。

### 构建与文档
- 更新 Maven 编译、测试、代码分析、覆盖率及发布插件。
- 完善模块架构、公共 API、本地开发、测试与发布文档。

### 升级说明
- 本次主要更新依赖、构建配置和文档，未修改分页与 SQL 插件实现。
