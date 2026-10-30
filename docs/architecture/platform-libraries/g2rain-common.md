# g2rain-common 平台共享库登记

## 登记信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [g2rain/g2rain-common](https://github.com/g2rain/g2rain-common) |
| 平台身份 | Supporting Library |
| 实现类型 | 公共 Java JAR |
| 当前版本 | `1.0.9` |
| 发布 Tag | [v1.0.9](https://github.com/g2rain/g2rain-common/releases/tag/v1.0.9) |
| 发布核对日期 | 2026-10-30 |
| 项目文档 | [版本文档](https://github.com/g2rain/g2rain-common/tree/v1.0.9/docs) |

## 能力与边界

统一响应、分页、异常与错误码、JSON、主体上下文及 JWT/DPoP 公共模型。

Common 提供公共契约，不拥有业务域；消费方负责业务授权和运行配置。

中央仓库维护定位、发布版本与跨仓库关系；实现、配置、测试和升级细节由责任仓库维护。本次按 GitHub Release 和同名 Tag 登记，不据此修改消费项目采用的架构 Profile，也不推定 Maven Central 制品可用性或环境联调结果。

## 本轮更新

### 技术基线与生态适配
- 采用 JDK 25 技术基线，编译及运行环境需使用 JDK 25 或以上版本。
- 面向 Spring Boot 4 / Spring Framework 7 生态更新公共依赖。
- Jackson 升级至 3.2.4。
- Jakarta Validation 升级至 3.1.1，Hibernate Validator 升级至 9.1.5.Final，Expressly 升级至 6.0.0。

### 新增
- TokenJWTPayload 新增 entryId 字段，携带匿名接入码 ID，支持网关校验接入码是否仍启用。

### 调整
- 移除 BaseDto、BaseVo 时间字段上的固定 GMT+8 时区配置，保留 yyyy-MM-dd HH:mm:ss 格式。

### 依赖与构建
- 更新 Lombok、Swagger Annotations 及测试依赖。
- 更新 Maven 编译、测试、代码分析与发布插件。
