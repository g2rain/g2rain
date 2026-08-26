# 运行模式与平台协作

## 双模式

- 集成模式是平台正式入口：用户经 main-shell 使用 qiankun App，主应用传递 Token、Client、语言、初始路由和唯一实例键。
- 独立模式用于开发、诊断和必要的独立入口，App 自行完成 IAM SSO、Token 和资源初始化。
- 默认模式、URL 参数和环境变量的优先级由项目明确，不能让同一地址在不同环境产生不可解释行为。

## 平台关系

```text
Browser → main-shell → frontend App
frontend App → IAM（认证）
frontend App → Gateway → 领域服务（业务接口）
```

App 不直接绕过 Gateway 调用普通领域服务；IAM 的认证链路按安全设计独立处理。主应用提供的前端上下文不能替代 Gateway/服务端验证。

## 微前端生命周期

- mount 校验容器和实例键，并在业务资源/路由初始化前建立可信认证上下文。
- update 正确处理 Token、语言和初始路由变化，不重新污染其他实例。
- unmount 配对清理 Vue、Router、Watcher、事件和实例状态。
- 同 entry 多 Tab 使用主应用提供的唯一实例键隔离，不能退化为应用编码级全局单例。

## 资源路由

后端页面资源的 linkPath 与前端组件注册表保持一致。正式页面应使用构建器可静态分析的组件注册；任意字符串动态 import 不能作为可靠生产默认。

## 运行配置

构建期公开配置与容器运行期配置必须明确区分。Context Path、应用编码、SSO Redirect、main-shell entry/activeRule 等成对配置，并在独立与集成模式验证。
