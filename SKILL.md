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

普通用户默认没有任何既有 Banana Meet MCP 配置。Agent 先自行运行 `codex mcp get banana-meet --json`：

- 若服务不存在，直接运行不带 Token 参数的 `codex mcp add ...`，创建远程 Streamable HTTP 服务。
- 若发现任意遗留的同名配置不是目标远程地址，静默执行 `remove` 后再执行 `add`；不要向用户解释“stdio”或本地脚本。

服务不存在时只执行第二条；仅重建旧配置时依次执行两条：

```bash
codex mcp remove banana-meet
codex mcp add banana-meet --url https://banana.namihai.com/mcp
```

随后提供以下两种认证方式；不得要求用户在聊天中发送 Token。

- **Codex App**：在 **Settings → MCP Servers → banana-meet** 的 HTTP Headers / 请求头区域添加 `Authorization = Bearer 管理员提供的 Banana Meet Token`。
- **Codex CLI、VS Code 或其他 IDE**：编辑 `~/.codex/config.toml`，使用下列配置；配置文件方案和环境变量方案详见 [references/remote-mcp.md](references/remote-mcp.md)。

用户保存后，Agent 检查 `codex mcp get banana-meet --json`。有效配置必须存在 `http_headers.Authorization`，或配置了 `bearer_token_env_var`。若 App 将现有 Header 保存到 `env_http_headers.Authorization`，将同一条现有 Header 静默转换为 `http_headers.Authorization`；不得展示 Token。之后要求用户完全退出并重新打开 Codex App 或 IDE，再新开 task 并重新发送原始 Banana Meet 命令。认证会跨 task 保持；只有 Token、连接或地址失效时才重新配置。

首次接入完成后，向用户展示以下一次性使用说明；之后日常调用不再重复展示：

```text
Banana Meet 已就绪。

创建活动：
$banana-meet create <活动名称>，<日期或每周星期>，<时间安排>
示例：$banana-meet create 产品讨论，2026-08-20，09:00–18:00 可选
示例：$banana-meet create 产品讨论，下周所有日期，09:00–18:00 可选
示例：$banana-meet create 周会，每周三，14:00–15:00 固定

创建需提供活动名称、日期和时间安排；缺失时 Codex 会补问。
若不需要选择时间，请明确写“无需选择时间”。创建完成后会返回活动链接和活动码。

查看报名报告：
$banana-meet report <活动链接或活动码>
示例：$banana-meet report https://banana.namihai.com/event/ABCD

报告会返回参与人数和推荐时段。

使用 Codex CLI、VS Code 或其他 IDE 时，可将 MCP 写入 ~/.codex/config.toml；可直接保存 Authorization 请求头，或使用 bearer_token_env_var 读取环境变量。完整可复制配置见 Skill 的 remote-mcp 参考。
```

## create

使用 `$banana-meet create <活动描述>` 时，从描述中提取活动名称、具体日期或每周重复日期、以及时间安排。

调用 `create_event` 前必须确认以下信息完整：

- 活动名称；
- 日期（具体日期或每周重复的星期）；
- 时间安排（固定时段、参与者可选的时间范围，或明确“不需要选择时间”）。

任一项缺失、含糊或无法解析时，先用中文只询问缺失项；不要猜测日期、默认全天或擅自省略时间。例如：`请补充活动名称、日期，以及时间安排（如“09:00–18:00 可选”）。` 收齐后直接创建。

调用 `create_event` 后返回活动链接和活动码。

## report

使用 `$banana-meet report <活动链接或活动码>` 调用 `get_event_report`。说明参与人数，以及参与者重合度最高的最长连续推荐时段；无人报名时说明暂无法推荐。

完整 MCP 工具说明见 [references/remote-mcp.md](references/remote-mcp.md)。
