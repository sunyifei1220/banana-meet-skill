# Banana Meet 远程 MCP

端点：`https://banana.namihai.com/mcp`

认证：Bearer Token。

## 配置

### 配置文件：适用于 Codex、VS Code 与其他 IDE

编辑用户目录中的 `~/.codex/config.toml`。最简单的静态配置如下，Token 会以明文保存在该用户配置文件中：

```toml
[mcp_servers.banana-meet]
enabled = true
url = "https://banana.namihai.com/mcp"

[mcp_servers.banana-meet.http_headers]
Authorization = "Bearer <管理员提供的 Token>"
```

不要将该文件提交至代码仓库或分享给他人。保存后完全重启 Codex 或 IDE。

若不希望将 Token 写入配置文件，改用环境变量：

```toml
[mcp_servers.banana-meet]
enabled = true
url = "https://banana.namihai.com/mcp"
bearer_token_env_var = "BANANA_MEET_MCP_API_KEY"
```

在启动 Codex 或 IDE 的同一环境中设置 `BANANA_MEET_MCP_API_KEY`；例如使用 CLI 时：

```bash
export BANANA_MEET_MCP_API_KEY='管理员提供的 Banana Meet Token'
codex
```

IDE 必须从包含该环境变量的进程启动，或由系统环境变量提供该值。

CLI 注册命令：

```bash
codex mcp add banana-meet --url https://banana.namihai.com/mcp --bearer-token-env-var BANANA_MEET_MCP_API_KEY
```

可用工具：

- `create_event`：创建活动。可选 `webhookUrl` 为该活动绑定新报名回调。
- `get_event_report`：获取参与人数、可用时间统计与最高重合的连续推荐时段。
- `bind_webhook`：为已有活动绑定通用 HTTPS Webhook。

Webhook 使用 POST JSON 发送 `banana-meet.participant.created`，仅在新参与者首次报名时触发。不要在日志、提示或提交中公开 API Key 和 Webhook URL。
