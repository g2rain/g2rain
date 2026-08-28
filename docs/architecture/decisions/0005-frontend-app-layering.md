# ADR-0005：前端 App 采用分层单向依赖

## 状态

已接受（纳入 `frontend-app 1.0.0`）

## 背景

多个前端项目共享组件、Token、i18n、微前端和运行时能力。如果业务页面反向渗透到公共组件，或 platform 依赖具体 App runtime，模板复制会把循环依赖和业务耦合扩散到所有项目。

## 决策

前端 App 采用 `shared → components → platform → runtime → views` 层次，业务依赖从 views 向下。`main.ts`/`App.vue` 作为组合根装配各层。需要反向协作时使用 provider/interface、回调注入、props/events 或类型安全消息，不直接 import 上层实现。

## 影响

- 业务页面、API 和业务类型保留在 views/runtime。
- 通用 components 不直接读取 Store、页面 API 和领域字段。
- platform 不依赖具体 App runtime/views。
- 现有违反方向的 import 登记项目偏差并渐进迁移；不得作为新代码范例。
- 可复用子模块通过 `index.ts` 暴露稳定 Public API。

## 验证

架构 Review 检查新增/修改 import、公共导出、目录归属和项目偏差；未来可增加服务开发者和 CI 的依赖规则检查，但不添加只供 Agent 使用的脚本。
