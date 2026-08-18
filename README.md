# Banana Meet

在 Codex 中创建活动、收集报名时间、查看推荐时段。

## 第一次使用

1. 首次运行 `create` 或 `report` 时，Codex 会自动创建 `banana-meet` 远程 MCP。
2. 使用下列任一认证方式：

   **Codex App**：在 **Settings → MCP Servers → banana-meet** 中添加 HTTP Header：

   ```text
   Authorization = Bearer 管理员提供的 Token
   ```

   **Codex CLI、VS Code 或其他 IDE**：编辑 `~/.codex/config.toml`：

   ```toml
   [mcp_servers.banana-meet]
   enabled = true
   url = "https://banana.namihai.com/mcp"

   [mcp_servers.banana-meet.http_headers]
   Authorization = "Bearer <管理员提供的 Token>"
   ```

3. 完全重启 Codex App 或 IDE 后，新开一个 task，再使用下面任一命令。

不要把 Token 发到聊天中、提交到代码仓库，或分享该配置文件。若不希望 Token 写入配置文件，请使用 `bearer_token_env_var` 读取环境变量，见 [远程 MCP 配置](references/remote-mcp.md)。

## 创建活动

```text
$banana-meet create 产品讨论，2026-08-20，09:00-18:00 可选
```

Codex 会返回活动链接。

## 查看报告

```text
$banana-meet report https://banana.namihai.com/event/<活动码>
```

Codex 会返回参与人数和推荐时间段。

以后直接使用 `create` 或 `report` 即可。只有 Token、认证或连接失效时才需要重新设置。
