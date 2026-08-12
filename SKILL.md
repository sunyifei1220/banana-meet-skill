---
name: banana-meet
description: 在 Codex 中用 $banana-meet create 或 $banana-meet report 管理 Banana Meet。用于按需接入远程 MCP、创建活动、返回活动链接，或获取参与报告与推荐时段。
---

# Banana Meet

将 `$banana-meet` 之后的第一个词作为子命令：

```text
$banana-meet create <活动描述>
$banana-meet report <活动链接或活动码>
```

## 路由规则

- 第一个词为 `create` 或 `report` 时，执行该小节的流程；其余文本是该命令的参数。
- 没有子命令时，简要列出两项可用命令并请用户选择；不要猜测或自动执行。
- 使用自然语言但明确是在创建或查询 Banana Meet 时，也可映射到相应子命令。

## 首次 MCP 接入

加载 Skill 时不要预检。仅在 `create` 或 `report` 第一次需要调用 `banana-meet` MCP、但工具尚不可调用时，暂停原操作。

当前 Codex App 的 MCP Servers 页面只提供环境变量配置，不提供“为远程 MCP 保存静态 Bearer Token”的界面。仅添加 `BANANA_MEET_MCP_API_KEY` 环境变量不会让远程请求自动带上 `Authorization: Bearer`，因此不能完成 Banana Meet 的静态 Token 接入。明确告知该限制，然后给出以下可复制的终端方案。令用户仅在自己终端中将占位符替换为 Token；不得要求其在聊天中发送 Token：

```bash
export BANANA_MEET_MCP_API_KEY='在此粘贴管理员提供的 Token'
codex mcp remove banana-meet
codex mcp add banana-meet --url https://banana.namihai.com/mcp --bearer-token-env-var BANANA_MEET_MCP_API_KEY
```

说明该环境变量必须可被 Codex App 进程读取；执行后从同一环境重新启动 Codex App 或新开 task。若 `remove` 表示服务不存在，可忽略该行的错误并继续执行 `add`。成功接入后，不再重复显示该引导；只有认证、Token、连接或地址失效时才重新显示。

## create

使用 `$banana-meet create <活动描述>` 时，从描述中提取活动名称、具体日期或每周重复日期、可选时间范围。

在调用 `create_event` 前，必须询问用户是否需要新报名消息推送；已明确说明时无需重复询问。

- 用户不需要：不传入 `webhookUrl`，创建普通活动。
- 用户需要：请用户提供接收地址；确认其为公开 HTTPS Webhook 后，将其作为 `webhookUrl` 传入。

Webhook 接收 `banana-meet.participant.created` JSON 事件。不得默认启用通知，也不得猜测或复用用户未明确提供的 Webhook。

调用 `create_event` 后返回活动链接和活动码。

## report

使用 `$banana-meet report <活动链接或活动码>` 调用 `get_event_report`。说明参与人数，以及参与者重合度最高的最长连续推荐时段；无人报名时说明暂无法推荐。

用户在创建后明确要求绑定或更新通知时，调用 `bind_webhook`。说明仅首次报名会发送事件；已有参与者后续修改时间不会重复发送。

完整 MCP 工具说明见 [references/remote-mcp.md](references/remote-mcp.md)。

永远不要在回复中复述、展示或持久化 Webhook URL；将其视为密钥。
