# Banana Meet Codex Skill

让 Codex 用统一的命令接口配置 Banana Meet、创建活动和查询报名报告。

## 安装

在 Codex 中安装该仓库根目录的 Skill。

安装后，在运行 Agent 的环境中安全设置 API Key：

```bash
export BANANA_MEET_MCP_API_KEY='从管理员处获取的密钥'
```

然后新开一个 Codex task。Skill 将以下三种命令视作稳定接口：

```text
$banana-meet config
$banana-meet create 产品讨论，2026-08-20，09:00-18:00 可选。
$banana-meet report https://banana.namihai.com/e/<活动码>
```

`$banana-meet` 是主 Skill，后面的第一个词由同一个 `SKILL.md` 路由为 `config`、`create` 或 `report`。首次执行 `config` 后，请新开 Codex task 或重启 App，使 MCP 工具可用。

## 安全性

不要将 `BANANA_MEET_MCP_API_KEY` 或活动 Webhook URL 提交到仓库、发送到聊天或写入公开配置。
