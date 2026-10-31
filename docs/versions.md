# 版本与依赖

本页集中提供 G2rain 已登记的 Maven / npm 制品版本、安装方式与配套关系，供应用开发、依赖升级和 AI 引用使用。

**核对日期：2026-10-31。** 下表 19 个 Maven 坐标的指定版本 POM、5 个 npm 包的指定版本元数据均已从官方 Registry 成功读取。本页列出已核实的登记版本，不等同于随时变化的 `latest`；本次未逐个下载、安装或验证制品的完整传递依赖。

## 如何选择入口

| 你要做什么 | 选择 |
| --- | --- |
| 创建前端业务 App 或主壳 | npm `create-g2rain-app` |
| 在前端项目复用公共能力 | npm `@g2rain/theme`、`ui`、`http`、`platform` |
| 创建后端工程 | Maven 插件 `com.g2rain:g2rain-crafter` |
| 从数据库表生成业务代码 | Maven 插件 `com.g2rain:g2rain-generator-maven-plugin` |
| 接入后端公共或基础设施能力 | 按需选择 Common、MyBatis 扩展及具体 Starter |
| 部署平台应用或知识库 | [开发与部署入口](getting-started.md)，应用源码和容器交付渠道分别说明 |

## Maven 制品

以下 GroupId 均为 **`com.g2rain`**，指定版本已在 Maven Central 核实。点击版本可查看对应制品页。

| 坐标 | 已核实版本 | 类型 | 用途 |
| --- | --- | --- | --- |
| `com.g2rain:g2rain-common` | [1.0.9](https://central.sonatype.com/artifact/com.g2rain/g2rain-common/1.0.9) | `jar` | 公共模型、响应与错误契约 |
| `com.g2rain:g2rain-mybatis-extensions` | [1.0.5](https://central.sonatype.com/artifact/com.g2rain/g2rain-mybatis-extensions/1.0.5) | `pom` | MyBatis 扩展父 POM |
| `com.g2rain:g2rain-mybatis-extension` | [1.0.5](https://central.sonatype.com/artifact/com.g2rain/g2rain-mybatis-extension/1.0.5) | `jar` | MyBatis 扩展内核 |
| `com.g2rain:g2rain-mybatis-pagination` | [1.0.5](https://central.sonatype.com/artifact/com.g2rain/g2rain-mybatis-pagination/1.0.5) | `jar` | 分页与 count 插件 |
| `com.g2rain:g2rain-starter-mybatis-pagination` | [1.0.5](https://central.sonatype.com/artifact/com.g2rain/g2rain-starter-mybatis-pagination/1.0.5) | `jar` | 分页自动配置 |
| `com.g2rain:g2rain-spring-boot-starter` | [1.0.6](https://central.sonatype.com/artifact/com.g2rain/g2rain-spring-boot-starter/1.0.6) | `pom` | 平台 Starter 父 POM |
| `com.g2rain:g2rain-starter-aegis-core` | [1.0.6](https://central.sonatype.com/artifact/com.g2rain/g2rain-starter-aegis-core/1.0.6) | `pom` | Aegis 核心 POM |
| `com.g2rain:g2rain-starter-cache-sync` | [1.0.6](https://central.sonatype.com/artifact/com.g2rain/g2rain-starter-cache-sync/1.0.6) | `jar` | 缓存同步 |
| `com.g2rain:g2rain-starter-data-redis` | [1.0.6](https://central.sonatype.com/artifact/com.g2rain/g2rain-starter-data-redis/1.0.6) | `jar` | Redis 数据访问 |
| `com.g2rain:g2rain-starter-department-principal` | [1.0.6](https://central.sonatype.com/artifact/com.g2rain/g2rain-starter-department-principal/1.0.6) | `jar` | 部门主体增强 |
| `com.g2rain:g2rain-starter-feign-plus` | [1.0.6](https://central.sonatype.com/artifact/com.g2rain/g2rain-starter-feign-plus/1.0.6) | `jar` | Feign 扩展 |
| `com.g2rain:g2rain-starter-identity-client` | [1.0.6](https://central.sonatype.com/artifact/com.g2rain/g2rain-starter-identity-client/1.0.6) | `jar` | 身份与数据隔离接入 |
| `com.g2rain:g2rain-starter-mybatis-extensions` | [1.0.6](https://central.sonatype.com/artifact/com.g2rain/g2rain-starter-mybatis-extensions/1.0.6) | `jar` | MyBatis 扩展自动配置 |
| `com.g2rain:g2rain-starter-spring-doc` | [1.0.6](https://central.sonatype.com/artifact/com.g2rain/g2rain-starter-spring-doc/1.0.6) | `jar` | OpenAPI 文档接入 |
| `com.g2rain:g2rain-starter-stream-redis` | [1.0.6](https://central.sonatype.com/artifact/com.g2rain/g2rain-starter-stream-redis/1.0.6) | `jar` | Redis Stream 接入 |
| `com.g2rain:g2rain-starter-tracing-otel` | [1.0.6](https://central.sonatype.com/artifact/com.g2rain/g2rain-starter-tracing-otel/1.0.6) | `jar` | OpenTelemetry 追踪 |
| `com.g2rain:g2rain-starter-web-infra` | [1.0.6](https://central.sonatype.com/artifact/com.g2rain/g2rain-starter-web-infra/1.0.6) | `jar` | Web 基础设施 |
| `com.g2rain:g2rain-generator-maven-plugin` | [1.0.8](https://central.sonatype.com/artifact/com.g2rain/g2rain-generator-maven-plugin/1.0.8) | `maven-plugin` | 数据库分层代码生成插件 |
| `com.g2rain:g2rain-crafter` | [1.0.9](https://central.sonatype.com/artifact/com.g2rain/g2rain-crafter/1.0.9) | `maven-plugin` | 后端工程骨架与生成编排插件 |

`pom` 类型用于父工程或依赖配置，不能当作普通运行时 JAR；也不能仅凭名字将父 POM 当作可导入 BOM。具体继承和依赖装配按对应项目文档执行。`maven-plugin` 用于构建期工程或代码生成，不作为业务运行依赖引入。

### 引入公共库

在应用 POM 的 `dependencies` 中按需添加，例如：

```xml
<dependency>
    <groupId>com.g2rain</groupId>
    <artifactId>g2rain-common</artifactId>
    <version>1.0.9</version>
</dependency>
```

Starter 需选择实际 JAR 模块，例如 Web 基础设施：

```xml
<dependency>
    <groupId>com.g2rain</groupId>
    <artifactId>g2rain-starter-web-infra</artifactId>
    <version>1.0.6</version>
</dependency>
```

上述片段展示制品坐标；接入还需按应用管理 Spring Boot 等框架依赖、配置和基础设施。不要仅因某版本可下载就认为任意依赖组合均已验证。

### 使用生成插件

准备目标目录与配置，按 [Crafter 使用说明](https://github.com/g2rain/g2rain-crafter/tree/v1.0.9#readme)创建工程：

```bash
mvn com.g2rain:g2rain-crafter:1.0.9:bootstrap
```

已有项目独立使用 Generator 时，按版本文档配置数据库和输出目录：

```bash
mvn com.g2rain:g2rain-generator-maven-plugin:1.0.8:generate -Dconfig.file=codegen.properties
```

生成代码后审查目标文件，再构建和验证业务。生成工具的覆盖规则、数据库参数和完整命令见各仓库 README。

## npm 制品

以下指定版本已在 npm Registry 核实。Appkit 根工作区不作为 npm 包发布，应选择四个子包。

| 包名 | 已核实版本 | 用途 |
| --- | --- | --- |
| `create-g2rain-app` | [1.0.1](https://www.npmjs.com/package/create-g2rain-app/v/1.0.1) | 创建 App / Shell，生成 CRUD 与权限资源 |
| `@g2rain/theme` | [1.0.1](https://www.npmjs.com/package/@g2rain/theme/v/1.0.1) | 设计变量、亮暗主题与样式 |
| `@g2rain/ui` | [1.0.1](https://www.npmjs.com/package/@g2rain/ui/v/1.0.1) | Vue / Element Plus 公共组件 |
| `@g2rain/http` | [1.0.1](https://www.npmjs.com/package/@g2rain/http/v/1.0.1) | HTTP、认证刷新、错误与 SSE |
| `@g2rain/platform` | [1.0.1](https://www.npmjs.com/package/@g2rain/platform/v/1.0.1) | 主子应用契约与运行能力 |

### 创建前端工程

在新项目的父目录选择一种工程类型：

```bash
# 业务子应用
npx create-g2rain-app@1.0.1 app g2rain-demo-app --context-path demo

# 主壳
npx create-g2rain-app@1.0.1 shell g2rain-demo-shell --context-path admin --port 3000
```

进入生成目录后执行 `npm install`、`npm run dev`，并按生成项目 README 配置运行模式、认证和网关。CLI 使用包内固定模板快照，App / Shell Template 是模板源码仓库，不是这两个创建命令所需额外安装的 npm 包。

### 安装前端公共包

按需要选择包，也可安装完整组合：

```bash
npm install --save-exact @g2rain/theme@1.0.1 @g2rain/ui@1.0.1 @g2rain/http@1.0.1 @g2rain/platform@1.0.1
```

公开入口、样式引入、Provider 和 Adapter 装配见 [Appkit v1.0.1](https://github.com/g2rain/g2rain-appkit/tree/v1.0.1#readme)。安装包不自动完成认证、权限或主子应用接入。

## 环境与配套关系

| 组件组 | 登记组合 | 环境与接入要求 |
| --- | --- | --- |
| 后端公共能力 | Common 1.0.9、MyBatis Extensions 1.0.5、Starter 1.0.6 | JDK 25；Starter 及 Crafter 生成项目的本轮基线为 Spring Boot 4.1.2 / Spring Cloud 2025.1.4 |
| 后端生成 | Crafter 1.0.9 → Generator 1.0.8 | JDK 25、Maven；生成参数、数据库连接和构建验证见责任项目 |
| 前端工程 | CLI 1.0.1 → App / Shell Template v1.0.1 → Appkit 1.0.1 | Node.js 22+；应用配置与运行依赖由生成项目管理 |
| UI | `@g2rain/ui` 1.0.1 | peer：Vue `^3.5.0`、Element Plus `^2.13.0`、图标包 `^2.3.2`、Theme `^1.0.1` |
| Platform Vue 入口 | `@g2rain/platform` 1.0.1 | Vue `^3.5.0`、Vue I18n `^11.4.4` 为可选 peer，使用相应入口时提供 |
| 知识库应用 | 知识服务 v1.0.0 + 运营端 v1.0.0 | 按应用版本文档部署；不将 GitHub Release 直接视为 Maven、npm 或镜像已发布 |

表中是当前登记的配套关系，真实业务应用、数据库与外部服务仍需按环境验证。应用版本、制品版本和架构 Profile 版本独立维护，升级依赖不自动改变架构采用状态。

## 区分版本与发布渠道

- **源码版本**：POM 或 package.json 中声明的版本，不能单独证明制品可下载。
- **GitHub Release / Tag**：固定项目源码与发布说明，不自动代表所有渠道已发布。
- **Maven Central / npm**：制品渠道，以对应坐标或包名的指定版本实际存在为准。
- **镜像与应用部署包**：按责任项目的镜像仓库、标签和部署文档核对。

团队接入时固定已验证版本并提交锁文件或依赖管理配置；升级后记录配套版本与验证结果。Registry 暂时访问失败应记录为“未核实”，不能据此直接认定未发布。

## 更新与资料入口

新增发布时，先核对官方 Registry 的指定版本，再更新本页及[中央登记](architecture/catalog/projects.yaml)，记录核对日期。历史 Release 说明保留其当时版本，不随本页修改。

[前端发布介绍](releases/ecosystem-v2026.10.29.md) · [后端发布介绍](releases/backend-ecosystem-v2026.10.30.md) · [开源协议](licenses.md) · [开发与部署](getting-started.md)
