# Banana Meet Codex Skill

让 Codex 通过远程 MCP 创建 Banana Meet 活动、查询报名报告，并按需绑定新报名 Webhook。

## 安装

在 Codex 中要求安装该仓库的 `banana-meet-skill` 目录，或使用 Codex Skill Installer 从 GitHub 安装。

安装后，在 Codex 环境中安全设置 API Key：

```bash
export BANANA_MEET_MCP_API_KEY='从管理员处获取的密钥'
```

然后新开一个 Codex task，使用：

```text
使用 $banana-meet 创建一个产品讨论活动。
```

首次调用时，Skill 会检查并注册远程 MCP。详情见 [远程 MCP 参考](references/remote-mcp.md)。

## 安全性

不要将 `BANANA_MEET_MCP_API_KEY` 或活动 Webhook URL 提交到仓库、发送到聊天或写入公开配置。
