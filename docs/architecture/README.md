# g2rain 组织级架构基线

本目录是 g2rain 多仓库项目的组织级架构事实来源，用于让同类型项目共享稳定、可版本化的架构规则。

## 核心模型

```text
组织级架构 Profile
        ↓
项目声明采用的 Profile 与版本
        ↓
项目领域文档 + 显式架构例外
        ↓
Agent 根据 Profile、项目文档和 Git Diff 审核
```

架构一致不代表所有项目完全相同。Profile 管理同类项目必须遵守的公共边界，项目只维护领域职责和经过说明的差异。

唯一但会影响多个项目的平台级工具不强行抽象为 Profile，而是在 `platform-tools` 中登记其研发契约。持续运行并提供组织级运行或安全契约的唯一服务登记在 `platform-services`；两者都不因只有一个实现而创建空泛 Profile。

## 当前 Profile

| Profile | 状态 | 适用项目 |
| --- | --- | --- |
| [java-domain-service](profiles/java-domain-service/README.md) | `1.0.0`（正式） | `g2rain-member` 已接入，`g2rain-department` 计划接入 |
| [frontend-app](profiles/frontend-app/README.md) | `1.0.0`（正式） | 模板与 Manager App 已完成首轮验证，项目按迁移流程显式接入 |
| [frontend-shell](profiles/frontend-shell/README.md) | `1.0.0`（正式） | `g2rain-main-shell` 已采用；继承 frontend-app 通用规则并增加主应用契约 |

后续可增加 `java-platform-service`、`java-library`、`spring-boot-starter` 和 `security-service`，不能把领域服务或前端规则强加给不适用的仓库。

主应用不作为普通 `frontend-app` 强行接入。`frontend-shell` 通过显式基础 Profile 复用通用前端规则，并独立治理微应用生命周期、全局导航、跨应用上下文及 Shell 安全边界。

## 平台工具

| 工具 | 状态 | 中央职责 |
| --- | --- | --- |
| [g2rain-app-cli](platform-tools/g2rain-app-cli.md) | 官方唯一前端项目脚手架，正式兼容组合待发布 | 维护其平台定位以及 CLI、模板、生成 App 与 `frontend-app` Profile 的关系 |

## 平台唯一服务

| 服务 | 状态 | 中央职责 |
| --- | --- | --- |
| [g2rain-iam](platform-services/g2rain-iam.md) | Platform Singleton；源码已核对 | 维护统一认证、授权、Token、IdP 以及 Main Shell、Gateway、Basis 之间的安全契约 |

IAM 持续运行并对外提供安全协议，因此不归入 `platform-tools`。当前只有一个实现，先按平台唯一服务治理；出现可替代实现或多仓库复用结构后再评估 `identity-security-service` Profile。

## 版本策略

- Draft 阶段使用 `1.0.0-draft` 并在试点分支验证。
- 正式基线使用 `architecture-v<major>.<minor>.<patch>` Git Tag，例如 `architecture-v1.0.0`。
- 文案澄清使用 Patch；向后兼容的新规则使用 Minor；改变模块或依赖边界使用 Major。
- 项目必须固定基线版本，不能长期只引用中央仓库 `main`。

`java-domain-service 1.0.0` 是首个正式基线，发布日期为 `2026-08-24`，对应固定引用 `architecture-v1.0.0`。

`frontend-app 1.0.0` 于 `2026-08-25` 转为正式版本，目标固定引用为 `architecture-v1.1.0`。模板和真实业务 App 的生产构建已经通过；各项目及脚手架兼容组合仍按迁移与发布流程显式升级，不能因 Profile 正式发布而自动改写项目声明。

`frontend-shell 1.0.0` 于 `2026-08-26` 转为正式版本，固定引用为 `architecture-v1.2.0`。当前只有 `g2rain-main-shell` 一个实现，因此以其项目文档、元数据、链接检查和生产构建作为首发验证依据；后续跨应用协议变更仍须补充真实子应用浏览器联调。

Profile 版本与中央仓库快照 Tag 是两个维度：Profile 独立演进语义版本，`architecture-v*` Tag 固定一次中央仓库完整快照。Draft 可以引用明确试点分支；正式采用时必须切换到包含该 Profile 的新固定 Tag，不能复用不含它的旧 Tag。

## 目录

- `profiles`：按项目类型组织的架构规则。
- `decisions`：影响多个项目的组织级 ADR。
- `catalog`：项目、Profile、版本和接入状态。
- `migrations`：基线升级和批量迁移流程。
- `platform-tools`：唯一或共享平台工具的定位、跨仓库契约和兼容关系。
- `platform-services`：持续运行的平台唯一服务、跨仓库运行契约和安全边界。

## 项目接入

项目的 `docs/project.yaml` 至少声明：

```yaml
architectureBaseline:
  repository: https://github.com/g2rain/g2rain
  ref: architecture-v1.0.0
  profile: docs/architecture/profiles/java-domain-service
  version: 1.0.0
  deviations: docs/architecture/deviations.md
```

Draft Profile 试点时可以把 `ref` 固定到明确试点分支，并同时声明 `status: pilot`；Profile 正式发布后切换到新 Tag。

项目根 `AGENTS.md` 应要求 Agent 在编码和审核前读取中央 Profile、本地项目元数据、架构例外和当前需求。项目不能用本地规则静默覆盖中央基线；确需偏离时必须登记例外，重大长期偏离应增加 ADR。

## 变更流程

```text
提出组织级问题
→ 编写或修改中央 ADR
→ 更新 Profile Draft
→ 选择代表项目试点
→ Agent 执行一致性审核
→ 发布架构 Tag
→ 各项目显式升级基线版本
```
