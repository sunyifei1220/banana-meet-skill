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
使用 $banana-meet /banana-meet config
使用 $banana-meet /banana-meet create 产品讨论，2026-08-20，09:00-18:00 可选。
使用 $banana-meet /banana-meet report https://banana.namihai.com/e/<活动码>
```

`/banana-meet` 是这项 Skill 的命令语法；Codex 目前通过 `$banana-meet` 显式加载 Skill，Skill 本身不能注册 UI 原生斜杠命令。首次执行 `config` 后，请新开 Codex task 或重启 App，使 MCP 工具可用。

## 安全性

不要将 `BANANA_MEET_MCP_API_KEY` 或活动 Webhook URL 提交到仓库、发送到聊天或写入公开配置。
