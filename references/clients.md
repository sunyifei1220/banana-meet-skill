# 客户端配置

所有客户端使用同一个远程 MCP：

```text
https://banana.namihai.com/mcp
```

认证密钥只从环境变量读取：`BANANA_MEET_MCP_API_KEY`。不要将其提交到 Git、写入公开配置或发送到聊天。

## Codex

```bash
codex mcp add banana-meet --url https://banana.namihai.com/mcp --bearer-token-env-var BANANA_MEET_MCP_API_KEY
```

## 通用 Streamable HTTP MCP 客户端

使用以下等价配置：

```json
{
  "mcpServers": {
    "banana-meet": {
      "url": "https://banana.namihai.com/mcp",
      "headers": {
        "Authorization": "Bearer ${BANANA_MEET_MCP_API_KEY}"
      }
    }
  }
}
```

如果客户端支持 `bearerTokenEnvVar`，优先使用它：

```json
{
  "mcpServers": {
    "banana-meet": {
      "url": "https://banana.namihai.com/mcp",
      "bearerTokenEnvVar": "BANANA_MEET_MCP_API_KEY"
    }
  }
}
```

不同 Agent 的配置字段名称可能不同，但目标始终是：以 `Authorization: Bearer <环境变量中的密钥>` 连接上述地址。
