# 平台共享库目录

本目录登记由 g2rain 组织统一提供、被多个项目作为版本化依赖消费的 Supporting Library。它们不拥有业务域，不是项目生成工具，也不作为独立平台服务持续运行。

## 与其他架构类型的区别

| 类型 | 核心作用 | 典型形态 | 治理重点 |
| --- | --- | --- | --- |
| Profile | 约束一类项目的共同架构边界 | 版本化规则集 | 结构、依赖和完成标准 |
| 平台工具 | 支撑研发、生成、验证或发布 | CLI、生成器 | 输入输出、生成契约和可复现性 |
| 平台唯一服务 | 在生产环境提供组织级运行契约 | 可部署服务 | 协议、安全和运行边界 |
| Supporting Library | 为多个项目提供版本化公共能力 | 公共 JAR、Starter、npm 包 | 公共 API、依赖方向、兼容性和发布 |

`g2rain-common`、`g2rain-spring-boot-starter` 和 `g2rain-appkit` 的平台角色相同，均属于 Supporting Library；技术栈和实现规则分别维护。

## 已登记共享库

| 共享库 | 状态 | 实现类型 | 主要消费者 |
| --- | --- | --- | --- |
| [g2rain-appkit](g2rain-appkit.md) | Supporting Library，库内实现已完成，Member 整体验证中，尚未发布 | npm workspace 公共包 | 前端 App、Main Shell、App Template |
| [g2rain-common](g2rain-common.md) | v1.0.9 已发布 | Java / Maven | 后端服务与研发工具 |
| [g2rain-mybatis-extensions](g2rain-mybatis-extensions.md) | v1.0.5 已发布 | Java / Maven | 后端服务与研发工具 |
| [g2rain-spring-boot-starter](g2rain-spring-boot-starter.md) | v1.0.6 已发布 | Java / Maven | 后端服务与研发工具 |

后端共享库版本于 2026-10-30 按 GitHub Release 与同名 Tag 核对；接入与验证状态见各自登记。

## Profile 提取条件

Supporting Library 不因技术栈不同而改变平台角色。只有在同一种实现类型中出现多个项目并形成稳定共同结构时，才分别评估 `java-library`、`spring-boot-starter` 或 `frontend-library` Profile；不创建混合 Java、Spring 和前端规则的空泛 Profile。
