# Banana Meet Codex Skill

让 Codex 用统一的命令接口创建 Banana Meet 活动和查询报名报告。

## 安装

在 Codex 中安装该仓库根目录的 Skill。

安装后，在运行 Agent 的环境中安全设置 API Key：

```bash
export BANANA_MEET_MCP_API_KEY='从管理员处获取的密钥'
```

然后新开一个 Codex task。Skill 将以下三种命令视作稳定接口：

```text
$banana-meet create 产品讨论，2026-08-20，09:00-18:00 可选。
$banana-meet report https://banana.namihai.com/e/<活动码>
```

`$banana-meet` 是主 Skill，后面的第一个词由同一个 `SKILL.md` 路由为 `create` 或 `report`。首次需要 MCP 时按需引导接入；接入成功后，直接使用这两个命令。只有认证、Token、连接或地址失效时才再次引导。

首次需要调用 MCP（`create` 或 `report`）而 MCP 尚未可用时，Skill 优先引导使用者在 Codex App 的 **Settings → MCP Servers** 添加 `banana-meet` 远程服务并填写 Bearer Token；只有该界面不可用或不支持静态 Token 时，才提供终端备用命令。成功接入后不会在后续操作中重复引导。

## 安全性

不要将 `BANANA_MEET_MCP_API_KEY` 或活动 Webhook URL 提交到仓库、发送到聊天或写入公开配置。
