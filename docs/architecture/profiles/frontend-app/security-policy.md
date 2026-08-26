# 安全边界

- Token、Refresh Token、Client 私钥、Cookie、验证码和生产 Secret 不写入前端源码、Bundle、公开运行配置、Mock、日志或仓库。
- `VITE_*` 和 `window` 运行配置对用户可见，只能包含公开配置。
- 集成模式主应用传递的 Token 仍由 Gateway/服务端验证；前端页面和按钮权限不是安全边界。
- SSO Redirect URI、Context Path、Origin 和状态参数精确配置，防止回调循环、开放重定向和跨应用污染。
- 多实例 mount/update/unmount 清理认证和监听状态，避免跨 Tab/用户复用。
- HTTP 签名覆盖字节、Content-Type 和实际请求一致；私钥由部署 Secret/Volume 提供。
- Mock、生成输入和示例不复制生产数据。
- 使用锁文件、依赖/镜像扫描和可追踪构建来源；高风险依赖升级说明兼容性。
- 安全漏洞通过私密渠道报告，不在公开 Issue 暴露利用细节。
