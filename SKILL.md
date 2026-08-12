---
name: banana-meet
description: 创建 Banana Meet 活动、读取参与报告、推荐时段并按需绑定通用 Webhook。用于用户要求创建、分享、汇总、推荐活动时间或接收 Banana Meet 新报名事件时；在新 Codex 环境中自动检查并配置远程 MCP。
---

# Banana Meet

使用 Banana Meet 远程 MCP：`https://banana.namihai.com/mcp`。

## 首次配置

先检查 `banana-meet` MCP 是否已存在且指向上述远程地址。若已正确配置，直接继续，不要重复安装。

若不存在或仍是本地 stdio 服务：

1. 要求用户在 Codex 进程环境中设置 `BANANA_MEET_MCP_API_KEY`。不得要求用户在聊天中发送密钥，也不得在回复中输出密钥。
2. 若已有同名但不是远程 MCP 的配置，先运行：

```bash
codex mcp remove banana-meet
```

3. 运行：

```bash
codex mcp add banana-meet --url https://banana.namihai.com/mcp --bearer-token-env-var BANANA_MEET_MCP_API_KEY
```

4. 提醒用户新开 Codex task 或重启 App，使新增 MCP 工具可用。

缺少 API Key 时，停止安装并请用户通过其安全的环境变量管理方式设置该变量。

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
