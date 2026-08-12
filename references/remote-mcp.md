# Banana Meet 远程 MCP

端点：`https://banana.namihai.com/mcp`

认证：Bearer Token，从环境变量 `BANANA_MEET_MCP_API_KEY` 读取。

静态 Token 的终端备用注册命令：

```bash
codex mcp add banana-meet --url https://banana.namihai.com/mcp --bearer-token-env-var BANANA_MEET_MCP_API_KEY
```

可用工具：

- `create_event`：创建活动。可选 `webhookUrl` 为该活动绑定新报名回调。
- `get_event_report`：获取参与人数、可用时间统计与最高重合的连续推荐时段。
- `bind_webhook`：为已有活动绑定通用 HTTPS Webhook。

Webhook 使用 POST JSON 发送 `banana-meet.participant.created`，仅在新参与者首次报名时触发。不要在日志、提示或提交中公开 API Key 和 Webhook URL。
