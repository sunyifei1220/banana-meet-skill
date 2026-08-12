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

首次需要调用 MCP 而服务尚未可用时，用户只需在 Codex App 的 **Settings → MCP Servers** 添加 `BANANA_MEET_MCP_API_KEY` 环境变量。确认保存后，Skill 会自行注册远程 MCP，并提示新开 task；用户不需要手动运行终端命令。只有该设置页无法保存环境变量时，Skill 才提供终端备用命令。

## 安全性

不要将 `BANANA_MEET_MCP_API_KEY` 或活动 Webhook URL 提交到仓库、发送到聊天或写入公开配置。
