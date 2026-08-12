# Banana Meet

在 Codex 中创建活动、收集报名时间、查看推荐时段。

## 第一次使用

1. 首次运行 `create` 或 `report` 时，Codex 会自动创建 `banana-meet` 远程 MCP 并打开授权网页。
2. 仅在该网页中输入管理员提供的 Token，确认授权。
3. 授权完成后新开一个 task，再使用下面任一命令。

不要把 Token 发到聊天、Codex 设置页或终端中。

## 创建活动

```text
$banana-meet create 产品讨论，2026-08-20，09:00-18:00 可选
```

Codex 会询问是否需要新报名通知，然后返回活动链接。

## 查看报告

```text
$banana-meet report https://banana.namihai.com/event/<活动码>
```

Codex 会返回参与人数和推荐时间段。

以后直接使用 `create` 或 `report` 即可。只有 Token、认证或连接失效时才需要重新设置。
