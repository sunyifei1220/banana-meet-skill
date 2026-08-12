---
name: banana-meet
description: 创建 Banana Meet 活动、读取参与报告、推荐时段并按需绑定通用 Webhook。用于任何支持 MCP 的 Agent 要求创建、分享、汇总、推荐活动时间或接收 Banana Meet 新报名事件时；优先适配 Codex，并提供通用客户端配置。
---

# Banana Meet

使用 Banana Meet 远程 MCP：`https://banana.namihai.com/mcp`。该 Skill 可用于任何支持 Streamable HTTP MCP 和 Bearer Token 的 Agent；客户端配置见 [references/clients.md](references/clients.md)。

## 首次配置

先检查 Agent 是否已注册名为 `banana-meet`、地址为上述远程 MCP 的服务。若已正确配置，直接继续，不要重复安装。

所有客户端均从进程环境变量 `BANANA_MEET_MCP_API_KEY` 读取密钥。不得要求用户在聊天中发送密钥，也不得将密钥写入 Skill、Git 仓库、配置文件或日志。

### Codex

若 Codex 中不存在或仍是本地 stdio 服务：

1. 若已有同名但不是远程 MCP 的配置，先运行：

```bash
codex mcp remove banana-meet
```

2. 运行：

```bash
codex mcp add banana-meet --url https://banana.namihai.com/mcp --bearer-token-env-var BANANA_MEET_MCP_API_KEY
```

3. 提醒用户新开 Codex task 或重启 App，使新增 MCP 工具可用。

### 其他 Agent

不要尝试执行 Codex 命令。使用 [references/clients.md](references/clients.md) 中对应客户端的 JSON 或 TOML 配置；统一使用远程地址和 `BANANA_MEET_MCP_API_KEY` 环境变量。

## 创建活动

收集活动名称、具体日期或每周重复日期，以及时间模式。

在调用 `create_event` 前，必须询问用户是否需要新报名消息推送。

- 用户不需要：不传入 `webhookUrl`，创建普通活动。
- 用户需要：请用户提供接收地址；确认其为公开 HTTPS Webhook 后，将其作为 `webhookUrl` 传入。

Webhook 接收 `banana-meet.participant.created` JSON 事件。不得默认启用通知，也不得猜测或复用用户未明确提供的 Webhook。

调用 `create_event` 后返回活动链接和活动码。

## 查询报告

使用活动码或链接调用 `get_event_report`。说明参与人数以及参与者重合度最高的最长连续时段；无人报名时说明暂无法推荐。

## 绑定 Webhook

仅在用户明确要求时调用 `bind_webhook`。说明仅首次报名会发送事件；已有参与者后续修改时间不会重复发送。

永远不要在回复中复述、展示或持久化 Webhook URL；将其视为密钥。
