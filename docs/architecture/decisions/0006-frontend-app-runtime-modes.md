# ADR-0006：前端 App 支持集成与独立双运行模式

## 状态

已接受（纳入 `frontend-app 1.0.0`）

## 背景

g2rain 业务 App 的正式入口由 main-shell 统一导航和装载，但开发、故障排查及部分场景需要子应用独立启动。只支持一种模式会降低平台一致性或本地可诊断性。

## 决策

前端 App 同时支持：

1. 集成模式：main-shell 通过 qiankun 装载并传递 Token、Client、Locale、初始路由和唯一实例键。
2. 独立模式：App 自行发起 IAM SSO、维护本地认证状态并加载资源。

正式平台访问优先集成入口。App 通过 IAM 认证，通过 Gateway 调用领域服务；前端上下文和权限 UI 不替代后端鉴权。

## 影响

- 运行模式选择和优先级必须文档化。
- mount 在资源加载前初始化可信认证上下文，update 支持 Token/Locale/Route，unmount 配对清理。
- 同 entry 多 Tab 使用唯一实例键隔离 Vue、Router 和监听状态。
- Context Path、Redirect URI、main-shell entry/activeRule 在两种模式验证。

## 验证

测试至少覆盖独立登录/回调、集成 mount/update/unmount、Token 过期、语言切换、多实例隔离、直链跳转和 Gateway/IAM 路径。
