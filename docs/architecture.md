# g2rain 总体架构设计

本文档从 **模块视角、调用链路和部署拓扑** 三个维度，介绍 g2rain 平台的总体架构设计。

---

## 🏗 架构总览

从上到下，将 g2rain 抽象为四层：

1. **Client 层**
   - 浏览器、移动端 H5 等
   - 只与前端应用交互，不直接感知后端细节
2. **Front Shell & Micro-Apps 层**
   - `g2rain-main-shell`（主应用 / 壳应用）
   - 基于 `g2rain-app-template` 构建的各类子应用
3. **Security & Gateway 层**
   - IAM / SSO 服务（如 `g2rain-iam`）
   - API Gateway（如 `g2rain-gateway`）
   - OpenResty + Lua 签名服务（如 `g2rain-lua-signer`）
4. **Business Services 层**
   - 各业务域后端服务 / 微服务集群

整体架构示意（逻辑）如下：

```text
Client (Browser)
   │
   ▼
g2rain-main-shell  ──────►  g2rain 子应用 (基于 g2rain-app-template)
   │                                  │
   │                                  │  DPoP / Token
   │                                  ▼
   ├────────────── HTTP / Token / DPoP ────────────────► API Gateway
   │                                                   │
   │                                                   ▼
   │                                      Business Services (后端服务)
   │
   └──────────────► SSO / OAuth2 / OIDC ◄────────────── IAM

                 ▲
                 │
              Lua 签名 / Application-DPoP (OpenResty)
```

---

## 🔐 认证与授权架构

### 1. 客户端密钥与 DPoP

**目标**：确保“是谁在发请求”、“请求是否可重放”，而不只依赖 Bearer Token。

1. **密钥生成**：
   - 前端首次访问时，在浏览器中使用 WebCrypto 生成 ES256 密钥对（P-256 曲线）。
   - 结果包含：
     - `clientId`：随机生成的客户端 ID。
     - `publicKey` / `privateKey`：JWK 格式。
2. **公钥上送**：
   - 在 SSO 授权流程中，前端会将 `publicKey` 随授权请求发送给 IAM。
   - IAM 在颁发 Token 时，将客户端相关信息写入 Token Payload。
3. **DPoP 请求**：
   - 针对每个 HTTP 请求，前端使用 `privateKey` 生成 DPoP JWT：
     - Header：算法、JWK、kid（通常为 clientId）。
     - Payload：`htu`（URI）、`htm`（Method）、`acd`（应用编码）、`pha`（Payload Hash）、`jti`（随机 ID）。
   - 后端或网关验证：
     - JWT 签名是否正确。
     - `htu`、`htm` 是否与当前请求匹配。
     - `jti` 是否在合理时效内且未被重放。

### 2. 应用级签名（Lua + ES256）

在某些场景下，仅有客户端 DPoP 仍不够，需要由“应用自身”对请求负责。  
g2rain 通过 OpenResty + Lua + luaossl，实现 **Application-DPoP**：

1. **密钥管理**：
   - OpenResty 容器中，`lua/keys` 目录存放：
     - `public-key.der` / `private-key.der`：应用公私钥（DER 格式）。
     - `iam-key-id.txt`：密钥 ID。
   - 通过 `lua/config.lua` 统一读取与缓存这些密钥。
2. **签名接口** (`/lua/sign_code`)：
   - 前端或网关调用该接口，将请求 Body/参数摘要发送给 Lua 服务。
   - Lua 计算 PHA、拼装 Payload，并使用应用私钥签名生成 JWT。
   - 生成的 JWT 会作为 `Application-DPoP` Header 附加到真正的业务请求中。
3. **后端验证**：
   - 通过应用公钥验证 Application-DPoP。
   - 确保请求确实由受信任的应用实例发出。

### 3. SSO 授权流程

SSO 流程与常见 OAuth2 授权码模式类似，关键差异在于：

- 回调时不仅下发用户 Token，还会下发与客户端、公钥、应用编码相关的信息。
- Token 中通常包含：
  - `clientId` / `clientPublicKey`
  - `applicationCodes`：当前用户允许访问的应用编码列表
  - `expireAt` / `refreshExpireAt`：过期时间

前端在 `SsoCallback` 页中完成：

1. 从 URL 提取 `code` 等参数。
2. 调用后端 `/auth/token` 交换 Token（携带 DPoP / Application-DPoP）。
3. 将 Token 写入 Pinia Store，并根据 `refreshExpireAt` 管理登录状态。

---

## 🧩 前端微前端架构

### 1. 主应用（Shell）职责

以 `g2rain-main-shell` 为代表，主应用承担：

- **身份与会话管理**：展示当前用户信息、处理登录/退出。
- **导航与布局**：左侧菜单、顶部导航、Tab 栏等。
- **子应用调度**：根据菜单配置动态加载不同子应用。

主应用通过 qiankun：

- 注册子应用（`registerMicroApps`）
- 配置 `activeRule` 与 `entry` 来决定某个路由/菜单下挂载哪个子应用

### 2. 子应用职责

以 `g2rain-app-template` 为代表，子应用只关注 **自身业务域**：

- 自己的路由树（`/system/user`、`/system/role` 等）。
- 自己的状态管理与 UI。
- 不直接持久化主应用下发的 Token（避免冲突）。

**Token 接收方式**：

1. 主应用在 `loadMicroApp` 或注册微应用时，通过 `props` 传递：
   - `token`：Token 字符串
   - `tokenKid`：与 IAM 公钥对应的 Key ID
2. 子应用在 `mount(props)` 生命周期中调用：
   - `initTokenFromProps(props)` 初始化 Pinia Store
3. 子应用内所有 HTTP 调用使用 Store 中的 Token + DPoP 进行鉴权。

### 3. 生命周期与运行模式

子应用在 `src/main.ts` 中同时导出：

- `bootstrap`：qiankun 初始化钩子
- `mount`：挂载，接收 props，初始化 Token 和应用实例
- `unmount`：卸载，清理挂载点与全局状态
- `update`：当 props 更新时（如 Token 变更）进行同步

并通过：

```ts
if (!(window as any).__POWERED_BY_QIANKUN__) {
  render();
}
```

来区分 **独立运行** 与 **子应用模式**。

---

## 🌐 网关与后端集成

### 1. API Gateway 角色

API 网关在 g2rain 中承担：

- 将前端的统一入口路径（如 `/api`、`/auth`、`/lua/sign_code`）转发到对应后端服务。
- 在必要时进行：
  - 基于 Token 的粗粒度鉴权
  - 基于 DPoP / Application-DPoP 的请求合法性校验
  - 访问日志与审计

### 2. 与 OpenResty 的协同

在一些部署方案中：

- OpenResty 同时承担静态资源服务（前端构建产物）与 Lua 签名接口的职责。
- Gateway 可以是独立组件，也可以与 OpenResty 组合部署。

典型部署路径：

- `/` 或 `/main`：前端主应用与子应用静态资源
- `/lua/sign_code`：Lua 签名接口
- `/keys/*`：获取 IAM 相关公钥与 keyId
- `/api/*`：转发到后端业务服务

---

## 📦 部署拓扑示例

典型生产部署可以类似于：

```text
Internet
   │
   ▼
[ Load Balancer / WAF ]
   │
   ▼
[ OpenResty + g2rain 前端 ]  ─────────►  [ API Gateway ]  ─────────►  [ Business Services ]
           │                                   │
           │                                   ▼
           └────────► [ IAM / SSO ] ◄──────────┘
```

说明：

- OpenResty 负责：
  - 前端静态资源加载（主应用 + 子应用）
  - Lua 签名接口与密钥管理
- API Gateway 负责：
  - 统一 API 入口
  - 请求路由与限流等策略
- IAM / SSO 负责：
  - 用户认证与 Token 签发

---

## 🔄 典型时序：用户访问子应用

以下是一个“用户未登录 -> 访问某个子应用页面”的简化时序：

1. 用户访问主应用（`g2rain-main-shell`）。
2. 主应用检测到当前无有效 Token：
   - 重定向到 SSO 认证中心。
3. 用户在 SSO 页面完成登录，SSO 将浏览器重定向回主应用的 `/sso_callback`：
   - 携带授权码 `code` 与 `clientId` 等信息。
4. 主应用前端调用 `/auth/token`：
   - 请求头中携带 DPoP 与 Application-DPoP（可选）。
   - 网关/后端验证通过后返回 Token。
5. 主应用将 Token 写入 Store，并根据菜单配置加载对应子应用（如系统管理）。
6. 主应用通过 qiankun `mount` 子应用：
   - 通过 `props` 传递 Token 与 TokenKid。
7. 子应用在 `mount(props)` 中：
   - 初始化自身 Store（`setTokens`）。
   - 渲染路由与页面。
8. 后续子应用内发起的 API 请求：
   - 通过 HTTP 工具自动附加 Token + DPoP。

---

## 🧭 总结

g2rain 的总体架构围绕三个关键点展开：

1. **安全可信**：通过 SSO + JWT + DPoP + Lua 签名，将安全边界前移并细化到每个请求。
2. **前后端解耦**：主应用 + 子应用的微前端模式，使不同业务团队可以独立演进。
3. **可扩展与可治理**：通过规范的仓库划分、文档与治理机制，让平台能在企业内部和社区中持续演进。

在此基础上，你可以：

- 进一步阅读各子项目 README 了解实现细节；
- 根据自身企业场景，裁剪或扩展其中的某些能力（如是否启用 Lua 签名、是否统一接入网关等）。


