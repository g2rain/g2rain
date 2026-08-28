# Components 与 Platform

## Components

通用组件满足无业务耦合、类型明确、可独立测试和稳定 Public API。服务路径、业务 DTO、具体 Store 和页面 API 通过 props/provider/adapter 从上层提供，不写入组件内核。

## Platform

平台层表达多个 g2rain App 共享的前端平台语义，例如 Token、Locale、i18n、HTTP 契约、微前端消息和错误分类。平台层不直接调用某个项目 runtime API 或 views。

## 能力归属

```text
只服务一个页面 → views
服务当前应用多个页面且含应用语义 → runtime
服务多个同类 App 且无领域语义 → platform
纯 UI、交互或基础协议 → components
无框架、无业务基础函数 → shared
```

下沉能力前必须去除业务耦合；下层出现业务条件时优先上移，不能为了“复用”建立反向依赖。

## 公共 API 兼容

模板和公共前端能力的导出变化会影响后续生成项目，也可能影响已复制代码的升级说明。修改导出、props、事件、消息、Store 或环境契约时说明兼容性和迁移方式。
