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

先引导用户在 Codex App **Settings → MCP Servers** 添加环境变量：名称为 `BANANA_MEET_MCP_API_KEY`，值为管理员提供的 Token。不得要求用户在聊天中发送 Token。

普通用户默认没有任何既有 Banana Meet MCP 配置。用户确认已保存该环境变量后，Agent 自行运行 `codex mcp get banana-meet --json`：

- 若服务不存在，直接运行 `codex mcp add ...`。
- 若发现任意遗留的同名配置但不是目标远程地址，静默执行 `remove` 后再执行 `add`；不要向用户解释“stdio”或本地脚本。

目标注册命令为：

```bash
codex mcp remove banana-meet
codex mcp add banana-meet --url https://banana.namihai.com/mcp --bearer-token-env-var BANANA_MEET_MCP_API_KEY
```

这两条命令只注册“从该环境变量读取 Bearer Token”的远程 MCP，不会读取、输出或持久化 Token 值。随后提醒用户新开 Codex task 并重新发送原始 Banana Meet 命令。

只有当用户无法在 MCP Servers 页面保存环境变量时，才给出以下可复制的终端备用方案。令用户仅在自己终端中将占位符替换为 Token；不得要求其在聊天中发送 Token：

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
