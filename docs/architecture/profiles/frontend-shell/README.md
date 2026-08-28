# 前端 Shell Profile

版本：`1.0.0`　状态：正式

该 Profile 适用于承载多个微前端子应用的 Vue 主应用。它以 [frontend-app 1.0.0](../frontend-app/README.md) 为通用前端基础，补充主应用独有的应用注册、生命周期编排、全局导航、运行上下文和跨应用安全契约。

普通 qiankun 子应用继续采用 `frontend-app`，不能因为接入主应用而采用本 Profile。当前唯一实现与首个正式验证对象为 `g2rain/g2rain-main-shell`。

## 继承关系

- 基础 Profile：`frontend-app 1.0.0`
- 本 Profile 仅覆盖主应用角色相关规则；未覆盖的分层、工程、测试、文档和安全规则继续继承基础 Profile。
- `shell` 是主应用组合与布局层，可依赖 runtime/platform/components/shared；业务 views 不得承载微应用编排核心。
- 项目必须显式记录基础 Profile、本 Profile 版本及中央快照，不能把继承关系理解为自动升级。

## 强制规则

1. 主应用是全局入口和编排者，不拥有子应用内部业务规则。
2. 子应用定义至少包含稳定的 `appKey`、运行时 `name`、`entry` 和 `activeRule`；配置必须校验唯一性和路径兼容性。
3. 挂载、更新、卸载和销毁必须可观察、可失败、可清理；同一子应用多实例必须使用稳定实例标识。
4. 菜单、Tab、浏览器路由和微应用实例状态必须有清晰的单一事实来源，避免双向更新回路。
5. 跨应用消息使用版本化、类型化信封；发送方、目标、来源、负载和权限必须校验。
6. Token 只按最小必要范围传递；子应用前端权限不替代 Gateway/IAM 的后端鉴权。
7. `entry`、`activeRule`、Context Path、Vite base 和 Nginx 路径必须作为一个部署契约共同验证。
8. 私钥和生产 Secret 不得进入前端 Bundle、公开运行时配置或仓库；OpenResty/Lua 签名能力必须单独审计密钥注入与轮换。
9. 主应用协议变更必须验证至少一个真实子应用，并记录兼容性与回滚方式。
10. Profile 或跨应用协议变更必须通过生产构建、文档校验；涉及运行契约时还必须完成真实子应用浏览器集成验证。

## 专题规范

- [生命周期与跨应用契约](shell-runtime-policy.md)
- [安全与部署边界](shell-security-policy.md)

## 正式版本

- Profile 版本：`1.0.0`
- 发布日期：`2026-08-26`
- 中央固定快照：`architecture-v1.2.0`
- 验证对象：`g2rain/g2rain-main-shell`
- 发布依据：项目文档、元数据和本地链接校验通过，`npm run build` 生产构建通过。

由于当前只有一个主应用项目，`g2rain-main-shell` 作为首个且唯一实现完成正式验证。新增第二个 Shell 实现或修改跨应用运行契约时，必须补充真实子应用浏览器联调，并根据兼容性决定 Profile 的版本升级级别。项目级安全债务继续登记在项目 `docs/architecture/deviations.md`，不应被正式版本状态隐藏。
