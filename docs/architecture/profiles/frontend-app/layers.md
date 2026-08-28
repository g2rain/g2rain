# 层次与依赖

## 目标模型

```text
views → runtime → platform → components → shared
```

箭头表示允许向下依赖，不要求逐层中转。views 可以直接使用 platform/components/shared，runtime 可以直接使用 components/shared。`main.ts` 和 `App.vue` 是组合根，可以装配各层，但不能成为隐藏业务逻辑的杂物层。

## shared

无业务、无应用状态的基础函数、环境读取、URL 和类型工具。构建期生成器可以位于独立 shared/tooling 子域，但不能被浏览器入口隐式执行。禁止依赖上层。

## components

跨页面或跨项目复用的 UI、交互和基础协议能力。通过 props、emits、slots、provider 或接口注入平台/业务能力，禁止直接引用 Store、业务 API、业务 DTO、runtime 或 views。

## platform

同类 g2rain App 共享的 Token、语言、i18n、微前端协议、HTTP 契约等平台语义。可以依赖 components/shared，禁止依赖某个应用的 runtime、页面或领域字段。需要远程实现时由 runtime/组合根注册 provider。

## runtime

当前应用的认证、HTTP 注入、资源加载、路由、初始化和平台适配组合。可以依赖 platform/components/shared，不应被下层反向调用，也不作为通用 UI 组件库。

## views

业务页面、页面 API、业务类型、局部组件和 Mock。可以使用下层能力；页面之间避免深度 import，真正共享的能力先去除业务耦合再下沉。

## 依赖反转

上层能力需要被下层调用时，按优先级使用 provider/interface、组合根注入、props/events 或类型安全消息。路径别名不改变层次方向，现有反向 import 必须登记项目偏差和迁移方向。

## Public API

可复用子模块通过 `index.ts` 暴露稳定接口，只导出调用方需要的组件、函数和类型。禁止外部深度导入解析器、私有 Store 结构和具体适配实现。
