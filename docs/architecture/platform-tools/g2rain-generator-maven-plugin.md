# g2rain-generator-maven-plugin 平台工具登记

## 登记信息

| 项目 | 内容 |
| --- | --- |
| 仓库 | [g2rain/g2rain-generator-maven-plugin](https://github.com/g2rain/g2rain-generator-maven-plugin) |
| 架构类型 | `backend-code-generation-maven-plugin` |
| 平台身份 | g2rain 官方后端数据库代码生成引擎 |
| 实现策略 | `organization-singleton` |
| 当前版本 | `1.0.6` |
| Maven Goal | `g2rain:generate` |
| 上层编排 | [`g2rain-crafter 1.0.7`](g2rain-crafter.md) |
| 关联 Profile | [`java-domain-service 1.0.0`](../profiles/java-domain-service/README.md) |

该插件读取 JDBC 表元数据，通过 MyBatis Generator 生成 PO/Mapper，并通过 FreeMarker 生成 API、DTO、VO、DAO、Service、Controller 和应用配置。它是开发期工具，不是生产服务，也不采用生成项目的运行时 Profile。

## 工具链关系

```text
java-domain-service Profile
        ↓ 约束生成结果
g2rain-crafter（项目入口、骨架、阶段编排）
        ↓ foundry
g2rain-generator-maven-plugin（表元数据到分层代码）
        ↓
API / Biz / Startup 项目代码
```

Crafter 和 Generator 均可发布独立版本。Crafter 必须固定 Generator 版本；Generator 模板或输出契约变化时，Crafter 需要重新完成兼容验证并发布新版本。

## 生成契约

- 显式 Maven 参数优先于 `codegen.properties`；
- 输入包括 basePackage、JDBC、表、overwrite 和数据隔离选项；
- `tables.overwrite=false` 默认保护已有非空文件；部分模板即使允许覆盖也应保持 `skipIfExists`；
- 表列类型、非空、长度、主键、逻辑删除和租户列会影响生成的验证与隔离代码；
- 输出路径必须规范化并限制在目标项目根，不能由 projectName、basePackage、表名或实体名逃逸；
- 数据库密码不得出现在日志、示例真实值、生成文件、测试快照或发布产物中；
- 生成结果需要人工 Review、测试和构建，Generator 不是领域事实来源。

## 版本组合

| Crafter | Generator | 目标 Profile | 状态 |
| --- | --- | --- | --- |
| `1.0.7` | `1.0.6` | `java-domain-service 1.0.0` | Crafter 单元测试通过；Generator 本轮验证待更新 |

## 最低验收

- `mvn test`、插件 descriptor 和模板渲染测试通过；
- 使用隔离数据库验证多表、类型、主键、逻辑删除、租户列、排除表和 overwrite；
- 在临时目录运行独立 `generate` Goal 和 Crafter foundry；
- 生成项目无未解析模板、路径逃逸、Secret 或意外覆盖，并能完成构建；
- 发布包内模板、示例、sources、javadoc、POM、签名和 Maven Central 坐标通过检查。

## 当前关注点

- 输出路径虽然转为绝对规范化路径，但尚需证明统一执行目标根包含与符号链接检查；
- 发布资源 `src/main/resources/codegen.properties.example` 含 `root123456` 示例密码，应改为明显无效占位符；
- 当前真实数据库生成测试被注释，单元测试不能替代端到端生成；
- Generator、Crafter、模板和 Profile 的兼容组合应机器可读并写入生成结果。

当前只有一个官方后端代码生成引擎，因此按平台唯一工具治理，不创建独立 Profile。
