---
name: banana-meet
description: 在 Codex 中用 $banana-meet init、$banana-meet create 或 $banana-meet report 管理 Banana Meet。用于首次配置远程 MCP、创建活动、返回活动链接，或获取参与报告与推荐时段。
---

# Banana Meet

将 `$banana-meet` 之后的第一个词作为子命令：

```text
$banana-meet init
$banana-meet create <活动描述>
$banana-meet report <活动链接或活动码>
```

## 路由规则

- 第一个词为 `init`、`create` 或 `report` 时，执行该小节的流程；其余文本是该命令的参数。
- 没有子命令时，简要列出三项可用命令并请用户选择；不要猜测或自动执行。
- 使用自然语言但明确是在创建或查询 Banana Meet 时，也可映射到相应子命令。

## 首次 MCP 接入

加载 Skill 时不要预检。仅在 `init`、`create` 或 `report` 第一次需要调用 `banana-meet` MCP、但工具尚不可调用时，暂停原操作并先给出 Codex App 图形界面引导：

1. 打开 Codex App **设置**，搜索并进入 **MCP Servers**。
2. 选择添加远程 MCP，填写名称 `banana-meet` 与 URL `https://banana.namihai.com/mcp`。
3. 若界面提供认证方式，选择 **Bearer Token**，将管理员提供的 Token 粘贴到 Token 输入框，保存并启用。
4. 提醒用户新开一个 Codex task，并重新发送原始 Banana Meet 命令。

若 MCP Servers 页面无法打开、没有添加远程服务入口，或没有 Bearer Token 输入框，明确说明当前 App 无法用图形界面保存静态 Token，并给出以下可复制的终端备用方案。令用户仅在自己终端中将占位符替换为 Token；不得要求其在聊天中发送 Token：

```bash
export BANANA_MEET_MCP_API_KEY='在此粘贴管理员提供的 Token'
codex mcp remove banana-meet
codex mcp add banana-meet --url https://banana.namihai.com/mcp --bearer-token-env-var BANANA_MEET_MCP_API_KEY
```

说明该环境变量必须可被 Codex App 进程读取；执行后从同一环境重新启动 Codex App 或新开 task。若 `remove` 表示服务不存在，可忽略该行的错误并继续执行 `add`。成功接入后，不再重复显示该引导；只有认证、Token、连接或地址失效时才重新显示。

## init

仅在用户明确输入 `$banana-meet init` 时，运行 `codex mcp get banana-meet --json`，检查是否已注册名为 `banana-meet`、地址为 `https://banana.namihai.com/mcp` 的远程 MCP。正确时直接说明已配置，不要重复安装。若发现 MCP 尚不可用，优先执行上方“首次 MCP 接入”的图形界面引导。

首次 `init` 成功后，在同一环境的后续 `create` 和 `report` 中不得再次运行配置检查，直接调用 MCP 工具。仅当 MCP 调用返回认证失败、Token 失效、无法连接或地址不匹配时，才提示用户重新执行 `$banana-meet init`；不要在每次调用前预检。

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

缺少 API Key 时停止初始化，请用户通过安全的环境变量管理方式设置该变量。

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
