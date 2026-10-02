# Shell 需求规格模板

状态：`Draft`。新建 Shell 或让 AI 生成 Shell 前，复制本模板到目标项目 `docs/requirements/`，填写后再开始生成。

```yaml
---
id: shell-<name>
title: <主应用名称>
status: 待开发
owner: <负责人>
shellKind: workspace-tabs # 或 single-workspace
projectName: <npm 包名/目录名>
applicationCode: <稳定应用编码>
contextPath: /<路径前缀>
architectureBaseline:
  frontendApp: <版本与中央快照>
  frontendShell: <版本与中央快照>
---
```

## 1. 目标

- 要解决的入口、导航或子应用编排问题：
- 目标用户和使用场景：
- 成功标准：

## 2. 非目标

- 不承载的子应用业务能力：
- 不在本次处理的 IAM、Gateway 或部署改造：

## 3. Shell 交互与工作区

| 项目 | 决策 |
| --- | --- |
| 布局与导航 | |
| 默认首页与 404 | |
| Tab/工作区策略 | |
| 同一子应用多实例 | 允许 / 不允许；实例键来源 |
| 路由与深链 | |
| Locale/主题 | |

## 4. 子应用定义

每个子应用都必须填写一行。`entry` 只能是已批准的环境配置或开发占位值；不能填写 Secret 或任意公网地址。

| appKey | name | entry | activeRule | applicationCode | 是否多实例 | 可信来源/环境 | 备注 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| | | | | | | | |

## 5. 会话、消息与安全

- IAM/SSO 接入和回调路径：
- Token 最小传递范围及刷新/失效处理：
- Gateway/API 代理边界：
- 消息事件、来源验证、目标实例和 requestId 规则：
- 公开运行时配置与密钥注入边界：

## 6. 部署与运行

- 开发端口、容器端口、Context Path、Vite base：
- 静态资源、Nginx/OpenResty、代理和环境配置来源：
- 健康检查、日志和可观测性：
- 回滚方式：

## 7. 验收与验证

- 构建命令：`npm run build`
- 真实联调子应用：
- 浏览器场景：冷启动、挂载、更新、关闭卸载、深链刷新、Token/Locale 变化、多实例（如允许）、entry 失败。
- 安全检查：Token/Secret 不泄露、entry 与消息来源受限、后端鉴权不被前端绕过。
- 未验证项与风险：
```
