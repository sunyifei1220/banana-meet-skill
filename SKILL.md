---
name: banana-meet
description: 在 Codex 中以 /banana-meet config、/banana-meet create 或 /banana-meet report 管理 Banana Meet。用于配置远程 MCP、创建活动、返回活动链接，或获取参与报告与推荐时段。
---

# /banana-meet

将用户输入解释为以下命令：

```text
/banana-meet config
/banana-meet create <活动描述>
/banana-meet report <活动链接或活动码>
```

在 Codex 中，Skill 的显式加载方式是 `$banana-meet`，而不是可由 Skill 自行注册的原生斜杠命令。因此提示词使用：

```text
使用 $banana-meet /banana-meet create 产品讨论，2026-08-20，09:00-18:00 可选。
```

也接受用户省略 `$banana-meet` 后直接输入的 `/banana-meet ...`；按同一命令语义处理。

## config

使用 `/banana-meet config` 时，先运行 `codex mcp get banana-meet --json`，检查 Codex 是否已注册名为 `banana-meet`、地址为 `https://banana.namihai.com/mcp` 的远程 MCP。正确时直接说明已配置，不要重复安装。

密钥只从 Codex 进程环境变量 `BANANA_MEET_MCP_API_KEY` 读取。不得要求用户在聊天中发送密钥，也不得将密钥写入 Skill、Git 仓库、配置文件或日志。

若不存在或仍是本地 stdio 服务：

1. 若已有同名但不是远程 MCP 的配置，先运行：

```bash
codex mcp remove banana-meet
```

2. 运行：

```bash
codex mcp add banana-meet --url https://banana.namihai.com/mcp --bearer-token-env-var BANANA_MEET_MCP_API_KEY
```

3. 提醒用户新开 Codex task 或重启 App，使新增 MCP 工具可用。

缺少 API Key 时停止配置，请用户通过安全的环境变量管理方式设置该变量。

## create

使用 `/banana-meet create <活动描述>` 时，从描述中提取活动名称、具体日期或每周重复日期、可选时间范围。

在调用 `create_event` 前，必须询问用户是否需要新报名消息推送；已明确说明时无需重复询问。

- 用户不需要：不传入 `webhookUrl`，创建普通活动。
- 用户需要：请用户提供接收地址；确认其为公开 HTTPS Webhook 后，将其作为 `webhookUrl` 传入。

Webhook 接收 `banana-meet.participant.created` JSON 事件。不得默认启用通知，也不得猜测或复用用户未明确提供的 Webhook。

调用 `create_event` 后返回活动链接和活动码。

## report

使用 `/banana-meet report <活动链接或活动码>` 调用 `get_event_report`。说明参与人数，以及参与者重合度最高的最长连续推荐时段；无人报名时说明暂无法推荐。

用户在创建后明确要求绑定或更新通知时，调用 `bind_webhook`。说明仅首次报名会发送事件；已有参与者后续修改时间不会重复发送。

完整 MCP 工具说明见 [references/remote-mcp.md](references/remote-mcp.md)。

永远不要在回复中复述、展示或持久化 Webhook URL；将其视为密钥。
