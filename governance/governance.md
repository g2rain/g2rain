# g2rain 治理与组织结构

本文件描述 g2rain 开源平台的治理模型、角色分工与决策流程，旨在在 **开放协作** 与 **稳定可控** 之间取得平衡。

---

## 1. 治理目标

- **开放**：欢迎社区用户参与使用、反馈和贡献。
- **透明**：决策过程与结果可追溯（Issue / PR / Discussions）。
- **可持续**：核心模块有稳定维护者，避免“无人响应”。
- **可演进**：通过 RFC / 提案机制推动架构与特性演进。

---

## 2. 角色与职责

### 2.1 Organization Owner（组织拥有者）

- 通常为最初发起人或核心团队。
- 职责：
  - 管理 GitHub Organization 级别的权限与仓库创建。
  - 任命 / 撤销各仓库的 Maintainer。
  - 处理严重安全问题与法律合规事项。
- 一般不参与日常开发细节评审（除非必要）。

### 2.2 Maintainer（维护者）

对某个仓库或某一类模块负主要责任，如：

- `g2rain-main-shell` Maintainer
- `g2rain-app-template` Maintainer
- `g2rain-app-cli` Maintainer

职责：

- 制定与维护本仓库的技术路线与 Roadmap。
- 审阅并合并 PR，负责最终技术决策。
- 维护发布流程与版本管理（Release / Tag / Changelog）。
- 组织并主持与本模块相关的讨论（Issues / Discussions / 会议）。

任命方式：

- 由现有 Maintainer 提名，经 Organization Owner 或多数 Maintainer 通过后生效。
- 也可以由高频贡献者逐步升任（见下文）。

### 2.3 Committer（提交者）

在某个或多个仓库中有持续贡献记录，并被赋予直接推送分支、合并部分 PR 的权限。

职责：

- 负责某些子模块或功能线的开发与维护。
- 审阅和合并低风险 PR（文档、示例、小修复）。
- 对自己的改动负责，必要时回滚问题提交。

任命方式：

- 由 Maintainer 基于贡献记录（PR 数量、质量、代码评审参与度等）提名。
- 经团队内部简单确认后授予相应仓库的写权限。

### 2.4 Contributor（贡献者）

任何通过 Issue、PR、文档或讨论参与项目的人，都是 Contributor。

职责：

- 报告 Bug、提交建议、修正文档、完善示例。
- 参与设计讨论和 RFC 评审。
- 可以长期参与后被提名为 Committer 或 Maintainer。

---

## 3. 决策流程

### 3.1 日常变更

适用于 Bug 修复、小特性、文档更新等：

1. 通过 Issue 讨论需求或直接提交 PR。
2. 至少 1 名 Maintainer 或 Committer 进行 Review。
3. CI 通过后由 Reviewer 或仓库 Maintainer 合并。
4. 如为潜在破坏性变更，应在 PR 中标注 `breaking change`，并更新 Changelog。

### 3.2 重要特性与架构变更（RFC）

适用于：

- 新的核心模块或子系统引入。
- 安全模型调整（认证 / 授权 / Token / DPoP 等）。
- 架构级别变更（微前端架构、部署拓扑等）。

流程：

1. 在 `docs/rfcs/` 中新增 RFC 文档草案。
2. 在本仓库或相关子仓库创建 Issue，链接到 RFC。
3. 通过 GitHub Discussions 或会议收集反馈，至少保持开放讨论 1–2 周（视影响范围而定）。
4. Maintainer 评估反馈，给出“接受 / 拒绝 / 修改后再评估”的结论。
5. 记录决策结果，合并 RFC 并在 Roadmap 中更新。

---

## 4. 发布与版本管理

### 4.1 版本规范

推荐采用 **语义化版本号（Semantic Versioning）**：

- `MAJOR`：兼容性破坏（Breaking change）
- `MINOR`：向后兼容的新特性
- `PATCH`：向后兼容的 Bug 修复

示例：`1.4.2`。

### 4.2 发布流程（以单个仓库为例）

1. 确认所有关键 Issue 已处理，CI 全部通过。
2. 更新 Changelog（记录本次版本变更内容）。
3. Maintainer 创建 Tag（如 `v1.4.2`），并触发 Release 流程：
   - 前端项目：构建产物并上传 Release Artifact 或发布到 npm。
   - Lua / 配置项目：打包并上传。
4. 在 Release Note 中链接相关 Issue / PR。

---

## 5. 安全与漏洞披露

### 5.1 安全问题报告

- 不建议在公开 Issue 中直接披露未修复的严重安全漏洞。
- 建议通过以下方式报告：
  - 专用安全邮箱（如 `g2rain_developer@163.com`，需实际配置）

处理流程：

1. 收到报告后，由 Security Contact / Maintainer 进行初步评估。
2. 根据严重程度确定是否需要紧急修复与安全公告。
3. 在修复完成并发布补丁后，再公开详细信息。

### 5.2 依赖安全

- 对关键仓库启用 Dependabot / Renovate 等依赖漏洞扫描。
- 对涉及加密、认证、网关等模块的依赖升级需谨慎评估。

---

## 6. 沟通渠道与会议

- **GitHub Issues**：Bug 报告、明确的功能需求。
- **GitHub Discussions**：设计讨论、问答、经验分享。
- **社区会议（可选）**：
  - 可定期（如每月一次）举行线上会议，讨论 Roadmap 与关键 RFC。
  - 会议纪要记录在 `community/meeting-notes/` 中。

---

## 7. 角色演进与退出

- **晋升**：
  - Contributor → Committer：基于持续、优质贡献，由 Maintainer 提名。
  - Committer → Maintainer：基于对模块的深度参与与责任承担，由现有 Maintainer & Owner 共同决定。
- **退出**：
  - 若某角色长期不活跃，可转为 Honorary 状态，保留署名，不再承担日常责任。
  - 对于违反行为准则或造成严重风险的情况，可由 Owner 及 Maintainer 共同决定撤销权限。

---

## 8. 行为准则

所有参与者应遵守项目的行为准则（Code of Conduct），包括但不限于：

- 尊重他人，避免人身攻击与歧视性言论。
- 技术争议以事实与数据为依据，避免情绪化争吵。
- 尊重时间与精力成本，不滥发无效 Issue / PR。

详细内容请参考：`community/CODE_OF_CONDUCT.md`。

---

本治理文档会随着社区与项目的发展不断更新，欢迎通过 Issue / PR 提出改进建议。