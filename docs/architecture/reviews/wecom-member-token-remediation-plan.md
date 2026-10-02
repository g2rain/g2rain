# 企业微信 MEMBER Token 跨模块 Review 与整改计划

## 1. 文档信息

| 项 | 内容 |
| --- | --- |
| 状态 | 待整改 |
| Review 日期 | 2026-09-14 |
| 适用范围 | `g2rain-common`、`g2rain-basis`、`g2rain-iam`、`g2rain-gateway-webflux`、`g2rain-gateway-webmvc`、`g2rain-member` |
| 关联能力 | 企业微信客服回调解密、`memberResolveCode` 换票、`SessionType=MEMBER` Token、Gateway MEMBER API 权限、下游会员主体与租户隔离 |
| 文档目的 | 固化 Review 结论，将问题拆成可独立实施、验证和关闭的整改项 |

本文是整改跟踪文档，不替代各仓库的需求、设计、安全边界和发布说明。实施某一整改项时，应在对应仓库创建或激活需求，并同步更新该仓库的设计与测试文档。

## 2. Review 范围与结论

本次 Review 覆盖以下完整链路：

```text
企业微信客服回调
  → IAM 验签与解密
  → IAM 签发 memberResolveCode
  → 客服模块调用 sync_msg 获得 msgid / external_userid
  → IAM 使用 memberResolveCode 换取 MEMBER Token
  → IAM 直连 Member 解析或创建会员
  → Gateway 验证 MEMBER Token 和租户级 MEMBER API 权限
  → Gateway 向下游透传 organId / memberId
  → 下游执行登录、租户隔离、对象级和业务级授权
```

Review 共确认 4 个待整改问题：

| ID | 优先级 | 问题 | 主要影响 |
| --- | --- | --- | --- |
| WMT-001 | P1 | MEMBER 请求可能被下游误判为后端服务调用 | 登录守卫和租户数据隔离可能被跳过 |
| WMT-002 | P1 | IAM 客服解密与会员换票缺少可验证的调用方认证和限流 | 非受信调用方可能使用敏感内部能力 |
| WMT-003 | P1 | `memberResolveCode` 未绑定可信消息或外部联系人 | code 持有者可为同租户任意 `externalUserId` 换取 Token |
| WMT-004 | P1 | Gateway MEMBER 权限缓存存在失效后回填旧快照的竞态 | 授权撤销后旧权限最长可能继续生效 6 小时 |

当前结论：上述问题关闭前，不应将企业微信 MEMBER Token 链路认定为满足生产安全边界。

## 3. 整改顺序

建议按以下顺序实施：

1. WMT-001：先修复主体语义和下游隔离，阻断数据越权风险。
2. WMT-002：为 IAM 敏感入口建立可验证的服务调用边界。
3. WMT-003：收紧换票协议，解决 code 的消息绑定和幂等问题。
4. WMT-004：完善权限失效的一致性，保证授权撤销及时生效。

WMT-002 与 WMT-003 可以在设计完成后并行开发，但协议联调和上线应作为一个发布批次。WMT-001 涉及公共契约，必须先发布 Common，再升级 Gateway 和下游服务。

## 4. 整改项

### 4.1 WMT-001：修复 MEMBER 主体的后端调用误判

#### 问题证据

- `g2rain-common` 的 `PrincipalContext.isBackEnd()` 以 `applicationId == null` 判断后端调用。
- IAM 签发 MEMBER Token 时只设置 `sessionType`、`organId`、`memberId`、名称和时间信息。
- MEMBER 请求在 Gateway 跳过 DPoP 和签名摘要流程，因此不会通过应用作用域补充 `applicationId`、`applicationOrganId`。
- MEMBER Token 未提供 `organType`；现有数据隔离处理器只有在主体不是后端调用且 `organType` 为租户类型时才启用。
- 下游 `LoginGuardInterceptor`、`IdentityParamInjector` 和 MyBatis 数据隔离处理器均会跳过被认定为后端调用的请求。
- `g2rain-member` 的标准 DAO 虽使用 `@DataIsolation`，但该注解会受上述公共判断影响。

#### 修复目标

1. 后端服务调用必须使用显式、可信且不可由外部请求伪造的语义，不得继续通过缺少 `applicationId` 推断。
2. `SessionType=MEMBER` 必须被识别为已认证的前台主体，而不是后端服务主体。
3. MEMBER 请求必须稳定携带可信 `organId`，并进入适合会员主体的租户隔离流程。
4. MEMBER 不得被映射为 `userId`、员工角色或员工数据权限主体。
5. Gateway WebFlux 与 WebMVC 的主体构建和请求头透传行为必须一致。

#### 建议方案

- 在 Common 中引入明确的调用来源/服务调用标记，或至少将 `isBackEnd()` 调整为同时考虑已认证会话类型；该标记只能由受信基础设施创建。
- 明确定义 MEMBER 的组织类型和应用上下文策略。若 MEMBER 不属于具体前端应用，仍需保证其不会因应用上下文为空而获得后端调用语义。
- 为数据隔离组件增加 MEMBER 分支：按可信 `organId` 隔离，不依赖 `userId` 或员工角色。
- 下游对象级授权必须同时校验 `PrincipalContextHolder.getMemberId()` 和资源所属 `organId`。
- 在 Gateway Token 解析完成后立即校验 MEMBER claim 形状：`organId > 0`、`memberId > 0`、`userId == null`、`passportId == null`。

#### 涉及仓库

| 仓库 | 主要改动 |
| --- | --- |
| `g2rain-common` | 主体契约、后端调用语义、公共测试、版本升级 |
| `g2rain-gateway-webflux` | MEMBER 主体构建、校验、透传及负向测试 |
| `g2rain-gateway-webmvc` | 与 WebFlux 等价实现和测试 |
| `g2rain-spring-boot-starter` | LoginGuard、参数注入和数据隔离对 MEMBER 的处理 |
| `g2rain-member` | 租户隔离与对象级授权集成验证 |
| 其他允许 MEMBER 访问的领域服务 | 按 MEMBER API 清单补对象级授权验证 |

#### 验收标准

- MEMBER 请求中 `applicationId` 为空时，`PrincipalContextHolder.isBackEnd()` 仍为 `false`。
- MEMBER 请求可以通过登录守卫的 MEMBER 分支，缺少或非法 `memberId` 时被拒绝。
- MEMBER 查询、更新和删除只能访问 Token `organId` 下的数据。
- 请求参数、Header 或 Body 中伪造其他 `organId` 不能扩大访问范围。
- MEMBER 请求不会进入 USER/PASSPORT 的角色权限和身份注入逻辑。
- WebFlux 与 WebMVC 对同一 Token 和请求产生一致的主体请求头。

#### 必测场景

- 合法 MEMBER Token、缺失 `applicationId`、合法 `organId/memberId`。
- MEMBER Token 混入 `userId` 或 `passportId`。
- MEMBER Token 缺少、使用零值或负值 `organId/memberId`。
- 会员跨租户按 ID 查询、分页查询、更新和删除。
- 外部请求伪造全部 `PrincipalHeaders`。
- 后端服务调用与 MEMBER 调用的回归对比。

### 4.2 WMT-002：保护 IAM 客服解密与会员换票入口

#### 问题证据

- `POST /auth/wecom/customer_service/decrypt` 与 `POST /auth/member/token` 当前是普通 Controller 入口。
- 当前仓库未提供针对这两个入口的服务身份认证和授权实现。
- 未发现针对这两个入口的专用限流。
- IAM 设计文档要求两个接口仅供受信客服模块调用，并明确要求调用方认证和限流。

#### 修复目标

1. 只有已登记的企业微信智能客服模块能够调用两个接口。
2. 调用方身份必须可验证、可轮换、可撤销和可审计。
3. `decrypt` 与 `token` 使用独立权限，避免一个能力自动获得另一个能力。
4. 对调用方、租户、失败类型和速率建立安全审计，不记录密文、code、Token 或解密正文。

#### 建议方案

- 优先采用平台统一的服务身份机制；可选实现包括 mTLS 服务身份、短期服务 JWT 或经 Gateway 验证的受限服务凭证。
- 不使用固定共享明文 Secret 作为长期方案；如过渡期必须使用，应支持哈希存储、轮换、过期和来源限制。
- 分别定义 `wecom:customer-service:decrypt` 与 `member:token:exchange` 权限。
- 按调用方和来源地址限流，并为连续认证失败、code 枚举和异常换票量告警。
- 明确直连 IAM 的网络入口，不把整个 `/auth/**` 作为内部白名单。

#### 涉及仓库

| 仓库 | 主要改动 |
| --- | --- |
| `g2rain-iam` | 调用方认证、授权、限流、审计和 Controller 集成测试 |
| `g2rain-basis` | 如服务身份或权限事实由 Basis 管理，提供最小查询契约 |
| 部署仓库 | mTLS、网络策略、Secret 注入与轮换配置 |
| 企业微信智能客服模块 | 携带服务凭证并处理认证失败 |

#### 验收标准

- 无服务凭证、无效凭证、过期凭证和错误权限均被拒绝。
- 仅有 decrypt 权限的调用方不能调用 token，反之亦然。
- 合法客服模块可完成 `decrypt → sync_msg → token` 全链路。
- 限流触发时不访问 Basis、Member 或 Token 签发组件。
- 日志和错误响应不包含回调密钥、解密正文、`memberResolveCode` 或 MEMBER Token。
- 部署验证能够证明 Member 内部接口与 IAM 敏感入口未暴露到非受信网络。

#### 必测场景

- 缺少、伪造、过期和已撤销的服务凭证。
- 权限范围错误。
- 单调用方突发和持续超限。
- 多实例下的认证撤销与限流一致性。
- 认证失败时敏感信息脱敏。

### 4.3 WMT-003：绑定 memberResolveCode、消息与外部联系人

#### 问题证据

- `MemberResolveCodeDto` 当前仅保存 `organId`、绑定编码、企业标识、回调类型和接入模式。
- `MemberAuthorizeService` 从请求体直接读取 `externalUserId`。
- 请求字段 `msgid` 未参与校验、幂等或审计关联。
- `MemberResolveCodeService.requireValid()` 只读取 Redis，不进行原子消费或消息级登记。
- 同一 code 可在有效期内为同租户多个任意 `externalUserId` 发起会员解析和 Token 签发。

#### 修复目标

1. code 只能用于其对应的已验证回调实例和租户。
2. 每个换票请求必须关联可信 `msgid`，同一消息重复请求返回一致结果或明确的幂等结果。
3. `externalUserId` 必须来自可验证的 `sync_msg` 结果，不能只信任普通请求字段。
4. code 多消息复用如果仍是业务要求，必须限定允许的消息集合、时间窗和调用方，不能退化为租户级万能换票凭证。

#### 待确认设计决策

在开发前从以下方案中选择一种并记录到 IAM 设计文档：

| 方案 | 描述 | 取舍 |
| --- | --- | --- |
| A：单消息 code | 客服模块取得消息后，为每条 `msgid + externalUserId` 请求 IAM 签发一次性换票凭证 | 安全边界最清晰，但增加一次受信调用 |
| B：回调批次 code + 消息登记 | code 可覆盖一次 `sync_msg` 批次；客服模块先向 IAM 登记签名后的消息清单，换票时校验清单 | 支持批量，但协议与状态更复杂 |
| C：受信服务签名断言 | 客服模块使用独立服务密钥对 `code + msgid + externalUserId + timestamp` 签名，IAM 校验并原子登记 | 无需预登记，但依赖完善的密钥和重放治理 |

不建议保留当前“仅以 code 绑定 organId、任意提交 externalUserId”的模型。

#### 实现要求

- Redis 操作应使用原子脚本或等价原子能力完成校验、登记和幂等结果读取。
- 幂等键至少包含 `organId + msgid`，并校验其对应的 `externalUserId` 不可变化。
- 相同 `msgid` 使用不同 `externalUserId` 必须拒绝并产生安全审计。
- code、msgid 和外部联系人标识在日志中只能使用摘要或必要的长度/尾号信息。
- 并发首次换票只能产生一个逻辑结果；不得因竞态创建孤立会员或无限生成 Token。

#### 涉及仓库

| 仓库 | 主要改动 |
| --- | --- |
| `g2rain-iam` | code 数据结构、换票协议、原子幂等、测试和文档 |
| `g2rain-member` | 保持 `organId + externalUserId` 幂等创建，并增加并发集成验证 |
| 企业微信智能客服模块 | 提供可信消息关联或签名断言 |

#### 验收标准

- 伪造、过期、错租户或错回调实例的 code 被拒绝。
- 同一 `msgid` 和 `externalUserId` 的重复请求不会产生不同会员身份。
- 同一 `msgid` 改用其他 `externalUserId` 被拒绝。
- 未经可信消息绑定的 `externalUserId` 不能换取 MEMBER Token。
- 并发请求不会签发多个逻辑会话 Token；若返回同一个缓存 Token，应保持返回字段一致。
- code 过期后，相关消息登记也不可继续换票。

#### 必测场景

- code 伪造、过期、跨绑定和跨企业使用。
- `msgid` 缺失、重复及与 `externalUserId` 冲突。
- 同一 code 的多消息合法复用。
- 并发相同消息和并发不同消息。
- Redis 操作部分失败、Member 超时和 Token 签发失败后的重试。

### 4.4 WMT-004：保证 MEMBER 权限失效一致性

#### 问题证据

- Gateway 的 `MemberPerm` 使用按 `organId` 的 Caffeine 缓存，过期时间为访问后 6 小时。
- `MEMBER_PERM` 失效事件只执行 `invalidate(organId)`。
- 已开始的 WebFlux `Mono` 或 WebMVC `CompletableFuture` 回源不会被失效操作作废；旧请求完成后仍会把旧快照写回缓存。
- Basis 的 `MemberPermSyncService` 在多个事务型写流程中直接递增版本并发送失效事件，缺少事务提交后发布的可见保证。
- Gateway 虽接收 `SessionApiPermissionVo.version`，当前没有用它防止旧快照覆盖新状态。

#### 修复目标

1. 权限变更提交后，旧快照不得重新进入缓存。
2. Gateway 必须能够识别并拒绝低于当前失效代次的回源结果。
3. Basis 只在权限事实提交成功后发布失效事件；事务回滚不得造成虚假版本推进。
4. WebFlux 与 WebMVC 使用相同的版本和失效语义。
5. 回源失败时保持 fail closed，不得回退 Passport/DefaultPerm。

#### 建议方案

- Basis 在数据库事务提交后递增或持久化权限版本并发布事件；优先使用事务后回调或可靠 outbox。
- 事件载荷扩展为 `organId + version`，并明确版本单调性、重复投递和乱序处理规则。
- Gateway 维护每个 `organId` 的最低可接受版本或本地 generation。
- 回源开始时捕获 generation，响应返回后只有 generation 未变化且版本不低于最低版本时才允许写缓存。
- 失效时同时推进 generation；仅取消 in-flight 请求不能单独解决已到达 Basis 但尚未返回的旧读取。
- 对空权限集合正常缓存；对网络错误、协议错误和租户不匹配不缓存。
- Gateway 必须校验响应中的 `sessionType == MEMBER`、`organId` 与请求一致。

#### 涉及仓库

| 仓库 | 主要改动 |
| --- | --- |
| `g2rain-basis` | 权限版本、事务提交后事件、事件契约和测试 |
| `g2rain-common` | 如果事件载荷作为共享契约，新增兼容 DTO 和序列化测试 |
| `g2rain-gateway-webflux` | generation/version 防旧值回填、乱序事件测试 |
| `g2rain-gateway-webmvc` | 与 WebFlux 等价实现和测试 |

#### 验收标准

- 在回源未完成时撤销权限，旧回源结果不会写入缓存。
- 先收到高版本、后收到低版本事件或响应时，高版本状态不被覆盖。
- Basis 事务回滚不发布可生效的权限变更事件。
- 授权、撤销、控制单元发布/停用、资源关系变化均触发正确机构的失效。
- 多 Gateway 实例最终收敛到同一权限版本。
- Basis 不可用时 MEMBER 请求失败关闭，不读取 DefaultPerm。

#### 必测场景

- `load old → invalidate → old load completes` 的确定性并发测试。
- `invalidate → new load → delayed old load`。
- 事件重复、乱序、延迟和丢失后的版本回源。
- Basis 事务提交与回滚。
- 空权限集合、Basis 超时和错误响应。
- 5 万租户缓存上限和高并发同租户 single-flight。

## 5. 跨仓库契约与发布顺序

### 5.1 WMT-001 发布顺序

1. 在 Common 定稿主体语义，补兼容测试并升级版本。
2. 发布并升级 Spring Boot Starter，确保 MEMBER 不再被当作后端调用且按租户隔离。
3. 升级 WebFlux 与 WebMVC Gateway，重建 MEMBER 主体并执行 claim 负向校验。
4. 升级 Member 和所有已开放 MEMBER API 的下游服务。
5. 完成跨租户集成测试后再开放 MEMBER 控制单元。

### 5.2 WMT-002/WMT-003 发布顺序

1. 定稿服务身份和换票协议，保留明确的兼容窗口。
2. IAM 先支持新旧协议，但旧协议只能在受控开关和受信网络内使用。
3. 升级企业微信智能客服模块，切换服务认证和消息绑定协议。
4. 关闭旧协议和兼容开关。
5. 验证旧 code、旧服务凭证和无 `msgid` 请求均被拒绝。

### 5.3 WMT-004 发布顺序

1. 定义兼容的权限事件版本契约。
2. Gateway 先支持旧事件和新事件，并具备本地 generation 防回填能力。
3. Basis 切换为提交后发布带版本事件。
4. 稳定后移除旧事件兼容逻辑。

## 6. 跨实现一致性清单

WebFlux 与 WebMVC 每次整改必须共同核对：

| 能力 | WebFlux | WebMVC |
| --- | --- | --- |
| JWT 签名与过期校验 | 待复验 | 待复验 |
| MEMBER claim 形状校验 | 待整改 | 待整改 |
| MEMBER 跳过 DPoP/摘要规则 | 已实现，待安全回归 | 已实现，待安全回归 |
| 外部主体 Header 清除 | 已实现，待负向回归 | 已实现，待负向回归 |
| `X-MEMBER-ID` 可信重建 | 已实现，待集成回归 | 已实现，待集成回归 |
| 按租户 MEMBER API 权限 | 已实现，存在失效竞态 | 已实现，存在失效竞态 |
| Basis 不可用时失败关闭 | 已实现，待故障回归 | 已实现，待故障回归 |
| 权限版本/乱序保护 | 待整改 | 待整改 |

## 7. 测试基线

Review 当日执行各仓库 `mvn test`，结果如下：

| 仓库 | 测试数 | 失败 | 错误 | 跳过 |
| --- | ---: | ---: | ---: | ---: |
| `g2rain-common` | 261 | 0 | 0 | 0 |
| `g2rain-basis` | 82 | 0 | 0 | 0 |
| `g2rain-iam` | 56 | 0 | 0 | 0 |
| `g2rain-gateway-webflux` | 126 | 0 | 0 | 0 |
| `g2rain-gateway-webmvc` | 23 | 0 | 0 | 0 |
| `g2rain-member` | 15 | 0 | 0 | 0 |

现有测试通过只能说明当前实现的已有断言成立，尚未覆盖 WMT-001 至 WMT-004 的跨模块安全语义和并发竞态。每个整改项关闭时必须新增对应的负向、并发和跨仓库集成测试。

## 8. 整改跟踪

| ID | 负责人 | 状态 | 需求/PR | 验证记录 | 关闭日期 |
| --- | --- | --- | --- | --- | --- |
| WMT-001 | 待分配 | 待开始 |  |  |  |
| WMT-002 | 待分配 | 待开始 |  |  |  |
| WMT-003 | 待分配 | 待开始 |  |  |  |
| WMT-004 | 待分配 | 待开始 |  |  |  |

允许的状态：`待开始`、`设计中`、`开发中`、`联调中`、`已验证`、`已关闭`、`已接受风险`。

整改项只能在以下条件全部满足后标记为 `已关闭`：

1. 对应仓库需求和设计已更新。
2. 实现及单元、负向、并发测试已合并。
3. 相关仓库构建通过。
4. 跨仓库联调通过并记录版本组合。
5. 部署、配置、监控和回滚方案已验证。
6. 安全 Review 确认原问题不可复现，且未引入新的身份或租户边界绕过。

## 9. 后续 Review 关注项

以下事项本次未判定为独立阻断问题，但应在逐项整改时一并确认：

- MEMBER Token 默认 30 分钟有效期内，会员冻结或删除是否要求即时撤销；如要求，应设计会话版本、拒绝列表或状态同步。
- MEMBER Token 是否需要标准 `iss`、`aud`、`iat`、`exp` 和唯一 `jti`，以及 Gateway 的严格校验规则。
- Token 缓存中的原始 JWT、Redis key 中的外部联系人标识是否满足敏感数据和保留周期要求。
- IAM→Member 无鉴权直连的已接受架构例外是否仍满足生产网络拓扑；一旦 Member 进入共享或非受信网络，必须引入服务身份认证。
- Basis 权限查询接口和 Member 内部解析接口是否被基础设施限制为仅受信服务可达。

