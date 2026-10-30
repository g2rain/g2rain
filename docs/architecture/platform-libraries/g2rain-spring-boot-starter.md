# g2rain-spring-boot-starter 平台共享库登记

## 登记信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [g2rain/g2rain-spring-boot-starter](https://github.com/g2rain/g2rain-spring-boot-starter) |
| 平台身份 | Supporting Library |
| 实现类型 | Spring Boot 自动配置套件 |
| 当前版本 | `1.0.6` |
| 发布 Tag | [v1.0.6](https://github.com/g2rain/g2rain-spring-boot-starter/releases/tag/v1.0.6) |
| 发布核对日期 | 2026-10-30 |
| 项目文档 | [版本文档](https://github.com/g2rain/g2rain-spring-boot-starter/tree/v1.0.6/docs) |

## 能力与边界

通过独立 Starter 按需接入 Web、身份与数据隔离、Redis、Feign、追踪、消息和 OpenAPI。

Starter 负责按条件装配平台能力，不作为独立服务运行；业务服务负责配置和授权策略。

中央仓库维护定位、发布版本与跨仓库关系；实现、配置、测试和升级细节由责任仓库维护。本次按 GitHub Release 和同名 Tag 登记，不据此修改消费项目采用的架构 Profile，也不推定 Maven Central 制品可用性或环境联调结果。

## 本轮更新

### 框架与核心依赖升级
- Spring Boot：4.0.5 → 4.1.2。
- Spring Cloud：2025.1.1 → 2025.1.4。
- MyBatis：3.5.19 → 3.6.0。
- MyBatis Spring Boot Starter：4.0.1 → 4.1.0。
- Jackson 3：3.1.1 → 3.2.4；统一管理 Jackson 2 依赖至 2.22.4。
- JDK 编译基线保持为 25。

### 登录校验调整
- 统一校验各类会话的主体 ID，仅在对应 ID 存在且大于 0 时通过登录校验。
- ANONYMOUS 会话需携带有效 userId，不再直接放行。
- 补充匿名会话登录校验测试，修正文档中的 LoginGuard 使用示例。

### Feign 日志优化
- 请求及响应 Debug 日志中的用户名称、组织名称支持 URL 解码，提升中文名称可读性。
- 解码仅作用于日志副本，保留请求及响应原始 Header。

### 其他依赖与构建更新
- g2rain-common：1.0.8 → 1.0.9。
- G2rain MyBatis 扩展及分页组件：1.0.4 → 1.0.5。
- Redisson：4.3.0 → 4.8.0。
- Caffeine：3.2.3 → 3.3.1。
- SpringDoc OpenAPI：3.0.2 → 3.2.0。
- 更新 Lombok、Mockito 及 Maven 构建、分析和发布插件。

### 升级注意
- 使用 ANONYMOUS 会话访问需要登录的接口时，必须提供大于 0 的 userId。
- 无需登录校验的接口可使用 @LoginGuard(require = false)。
