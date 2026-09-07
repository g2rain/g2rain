# ADR-0007：Gateway 统一治理入口安全与可信主体边界

## 状态

建议（纳入 `gateway-service 1.0.0-draft` 试点）

## 背景

Gateway 同时承担动态路由、凭据认证、DPoP/签名、API 权限、主体透传、响应适配和入口日志。过滤器顺序或数据所有权不明确时，容易产生认证绕过、身份头伪造、缓存漂移和敏感日志泄露。Java Domain Service Profile 明确不适用于 Gateway，需要独立基线。

## 决策

建立 `gateway-service` Profile，共同治理 WebFlux 与 WebMVC Gateway：

1. Gateway 是入口策略执行点，不拥有身份、权限、路由或领域主数据。
2. 过滤器顺序、认证分流、白名单和主体头作为跨服务安全契约治理。
3. 外部主体头默认不可信，仅转发验证后重建的最小上下文。
4. Gateway API 权限不替代下游服务的领域和数据级授权。
5. 动态事实由数据所有者全量加载并通过增量消息同步，内存缓存不是主数据源。
6. 敏感请求/响应与凭据默认禁止记录。

## 影响

- `g2rain-gateway-webflux` 作为首个试点，`g2rain-gateway-webmvc` 作为后续计划实现；两者分别记录响应式与 Servlet 实现差异。
- Gateway 安全协议变化需要 IAM、Basis、同步组件和至少一个下游服务协同验证。
- Profile 只规定共享入口契约；Reactor 非阻塞、Servlet Filter 等实现规则保留在项目文档和测试中。

## 转为已接受的条件

试点风险关闭、关键负向测试和外部联调完成，中央变更评审合并，并发布包含该 Profile 的 `architecture-v1.3.0` 固定标签。
