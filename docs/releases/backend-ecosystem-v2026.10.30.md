# G2rain（谷雨）开源智能体平台｜后端工具链｜从公共组件到业务工程，让开发更进一步

**复用公共能力，按需接入基础设施，从数据库表生成业务代码。** 围绕 G2rain（谷雨）开源智能体平台的业务应用开发，本次汇集五个后端项目的新版本，为 Java 服务开发提供从公共契约、运行时接入到工程生成的配套工具。

[访问官网](https://www.g2rain.com) · [探索平台](https://github.com/g2rain/g2rain) · [开始开发](https://github.com/g2rain/g2rain/blob/main/docs/getting-started.md)

## 本次发布

| 项目 | 版本与发布说明 | 开发价值 |
| --- | --- | --- |
| g2rain-common | [v1.0.9](https://github.com/g2rain/g2rain-common/releases/tag/v1.0.9) | 统一响应、分页、异常与错误码、JSON、主体上下文及 JWT/DPoP 公共模型。 |
| g2rain-mybatis-extensions | [v1.0.5](https://github.com/g2rain/g2rain-mybatis-extensions/releases/tag/v1.0.5) | MyBatis SQL 插件链、SQL 解析缓存、分页与 count，以及分页 Starter。 |
| g2rain-spring-boot-starter | [v1.0.6](https://github.com/g2rain/g2rain-spring-boot-starter/releases/tag/v1.0.6) | 通过独立 Starter 按需接入 Web、身份与数据隔离、Redis、Feign、追踪、消息和 OpenAPI。 |
| g2rain-generator-maven-plugin | [v1.0.8](https://github.com/g2rain/g2rain-generator-maven-plugin/releases/tag/v1.0.8) | 将表元数据生成为 PO/Mapper 和 API、DTO、VO、DAO、Service、Controller 等分层代码。 |
| g2rain-crafter | [v1.0.9](https://github.com/g2rain/g2rain-crafter/releases/tag/v1.0.9) | 创建 API/Biz/Startup 三模块工程，编排项目骨架与数据库业务代码生成。 |

## 一套配合使用的后端开发基础

Common 沉淀响应、错误、主体上下文等公共模型；MyBatis Extensions 提供分页和 SQL 插件扩展；Spring Boot Starter 将平台基础设施按需装配进业务服务。开发新服务时，Crafter 创建项目骨架，再调用 Generator 从数据库表结构生成业务代码，让团队将精力投入领域逻辑与业务体验。

**Crafter 创建工程 → Generator 生成分层代码 → Common 复用公共契约 → Starter 与 MyBatis 扩展接入运行能力。**

已有服务可按需选择公共库与 Starter，新项目可从 Crafter 开始。各模块独立维护版本，接入时按对应版本文档选择依赖和配置。

## 这次升级带来了什么

- **技术栈配套更新**：JDK 25；Starter 与 Crafter 生成项目更新至 Spring Boot 4.1.2、Spring Cloud 2025.1.4。相关组件更新 Jackson 3.2.4、MyBatis 3.6.0 和 MyBatis Spring Boot Starter 4.1.0。
- **生成引擎更新**：Generator 1.0.8 适配 MyBatis Generator 2.0.0 的配置 API 与元数据模型，Crafter 1.0.9 固定使用该版本引擎。
- **运行接入改进**：Starter 收紧登录校验，要求相应主体 ID 有效；Feign Debug 日志支持解码中文名称，并保留原始 Header。
- **公共模型完善**：Common 的 TokenJWTPayload 新增 entryId，供网关校验匿名接入码状态；DTO/VO 时间字段移除固定 GMT+8 时区配置。
- **工程依赖更新**：MyBatis 扩展更新缓存与序列化依赖；Crafter 更新生成项目的依赖管理、Jib 和固定补丁版本的 Java 运行时镜像。

## 从创建一个后端工程开始

准备 JDK 25 和 Maven，先查看 [Crafter v1.0.9 使用说明](https://github.com/g2rain/g2rain-crafter/tree/v1.0.9#readme)，配置工程参数后运行固定版本的插件：

```bash
mvn com.g2rain:g2rain-crafter:1.0.9:bootstrap
```

Crafter 通过 skeleton / foundry 阶段创建工程与生成代码。数据库、包名、目标目录等参数见对应版本文档；生成后审查代码和配置，再构建并接入你的业务环境。

## 升级前需要了解

- **匿名会话**：Starter 1.0.6 中，ANONYMOUS 访问要求登录的接口需提供大于 0 的 userId；无需登录的接口按文档使用 `@LoginGuard(require = false)`。
- **时间处理**：Common 1.0.9 不再在 BaseDto / BaseVo 时间字段固定 GMT+8，升级时核对应用 JSON 时区与客户端显示。
- **直接调用生成器 API**：`toTableConfiguration(context, tableName)` 改为 `toTableConfiguration(tableName)`；`toJavaModelGeneratorConfiguration()` 改为 `toModelGeneratorConfiguration()`。
- **已有工程**：Crafter 的技术栈和镜像更新应用于后续生成的项目；现有工程需显式合并配置并验证。

## 一起完善真实业务开发体验

欢迎 [Star 主仓库](https://github.com/g2rain/g2rain) 收藏项目入口，在 [Discussions](https://github.com/g2rain/g2rain/discussions) 分享业务场景，或在 [Issues](https://github.com/g2rain/g2rain/issues) 反馈接入与生成问题。

本次为主仓库的生态发布汇总，版本依据 2026-10-30 核对的 GitHub Release 与同名 Tag。各项目独立维护版本；制品下载、测试记录和配置以对应版本文档为准。本次文档更新未重新执行组件构建或跨服务联调，也不改变架构 Profile 版本。
