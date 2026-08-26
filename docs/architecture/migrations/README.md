# 架构基线迁移

## 接入流程

1. 根据仓库用途选择 Profile，不能仅按编程语言分类。
2. 读取当前源码、POM、配置、测试和项目文档。
3. 对比 Profile，输出符合项、缺失项和有意差异。
4. 在项目 `docs/project.yaml` 声明基线版本。
5. 将不能立即消除的差异登记到 `docs/architecture/deviations.md`。
6. Agent 动态检查链接、模块、依赖、命令和领域边界。
7. 项目构建和测试通过后更新中央项目目录状态。

## 基线升级

```text
中央 Profile 发布新版本
→ 为同 Profile 项目创建迁移 Issue
→ 项目评估变更和例外
→ 在 feature/fix 分支升级
→ 合并 develop 并在测试环境验证
→ 合并 main
```

不能通过批量覆盖项目专属 docs 完成迁移。自动化只能处理模板和机器可验证字段，领域语义与例外由 Agent 和维护者审核。

## frontend-app 1.0.0

`frontend-app 1.0.0` 的中央固定快照为 `architecture-v1.1.0`。从 Draft 或未声明状态接入时：

1. 审核 `shared → components → platform → runtime → views` 依赖方向和现有偏差。
2. 验证独立模式、qiankun 集成模式、多实例隔离和卸载清理。
3. 验证代码生成、资源配置生成、Context Path、认证入口和部署配置。
4. 执行 `npm run build`，记录警告和未验证的浏览器、主壳、IAM、Gateway 集成风险。
5. 将项目 `docs/project.yaml` 的 Profile 版本更新为 `1.0.0`，`ref` 固定为 `architecture-v1.1.0`，并同步 `AGENTS.md`、README 和架构偏差。
6. 完成项目 Review 与测试环境验证后，再把中央项目目录状态更新为 `adopted`。
