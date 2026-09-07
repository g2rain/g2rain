# Gateway Service Profile

版本：`1.0.0-draft`　状态：Draft 试点

该 Profile 适用于 G2rain 的统一入口 Gateway，包括 Spring Cloud Gateway Server WebFlux 与 WebMVC 实现。它治理动态路由、入口认证、请求完整性、API 权限、可信主体透传、响应适配、同步缓存与入口观测。

`g2rain-gateway-webflux` 是首个试点实现，`g2rain-gateway-webmvc` 是计划接入实现。Profile 规定两类网关的共享契约；Reactor 非阻塞链路、Servlet Filter 和容器线程模型等实现约束由各项目文档补充。IAM、领域服务、普通公共库和前端项目不适用本 Profile。

## 强制规则

1. Gateway 是平台入口和策略执行点，不拥有用户、权限、路由、Token 或领域数据主事实。
2. 请求执行模型必须与实现匹配：WebFlux 链路保持非阻塞，WebMVC 阻塞调用受线程池、超时和资源上限约束。
3. 全局过滤器顺序、白名单语义、认证分流、错误码和可信主体头是跨服务安全契约。
4. 静态 API Key 与 JWT/DPoP 可分流认证，但都必须经过适用的路由匹配与 API 权限边界。
5. 外部主体头一律不可信；只向下游发送 Gateway 验证后重建的最小主体上下文。
6. Gateway 的 API 权限不替代下游服务的租户、对象、状态和数据级授权。
7. 动态路由、服务注册、权限和令牌上下文从数据所有者加载；内存缓存不是主数据源。
8. 启动全量加载与增量同步必须定义一致性、幂等、乱序、失败恢复和并发语义。
9. Token、Cookie、API Key、DPoP、密钥、密码和敏感正文不得进入仓库或普通日志。
10. Profile、协议、过滤器链、数据源、配置和部署变化必须同步项目文档并执行测试与集成验证。

## 专题规范

- [过滤器链](filter-chain-policy.md)
- [路由与同步](routing-sync-policy.md)
- [安全边界](security-policy.md)
- [可观测性](observability-policy.md)
- [测试策略](testing-policy.md)
- [完成定义](definition-of-done.md)

## Draft 转正式条件

- `g2rain-gateway-webflux` 的项目文档、元数据和本地链接保持一致；
- `mvn test`、Maven Enforcer 及适用静态检查通过；
- 关闭试点项目中开发默认凭据、敏感请求日志和 JaCoCo 无执行数据问题；
- 补充 API Key、JWT、API 权限、白名单和主体边界的关键负向测试；
- 完成 `g2rain-gateway-webmvc` 的项目初始化与共享契约差异审核；
- 完成 Nacos、Basis、Infra、同步消息、IAM 契约和至少一个真实下游服务联调；
- 中央变更评审合并后发布 `architecture-v1.3.0` 固定标签。

Draft 期间项目固定到试点分支 `feature/g2rain-architectur-init`，不得宣称已采用正式版。
