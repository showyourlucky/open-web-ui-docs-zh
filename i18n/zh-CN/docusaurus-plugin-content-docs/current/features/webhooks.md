---
sidebar_position: 17
title: "🪝 Webhook 集成"
---

概述
--------

Open WebUI 提供了一个 webhook 功能，可以在新用户注册到您的实例时自动接收通知。只需向 Open WebUI 提供一个 webhook URL，当有新用户账户创建时，它就会向该 URL 发送通知。

在 Open WebUI 中配置 Webhooks
---------------------------------

您需要从支持 webhooks 的外部服务（如 Discord 频道或 Slack 工作区）获取一个 webhook URL。此 URL 将用于接收来自 Open WebUI 的通知。

要在 Open WebUI 中配置 webhooks，有两种选择：

### 选项 1：通过管理员界面配置

1. 以管理员身份登录您的 Open WebUI 实例。
2. 导航至 `管理员面板`。
3. 点击顶部的 `设置` 标签。
4. 在管理面板的设置中，导航到 `常规` 部分。
5. 找到 `Webhook URL` 字段并输入 webhook URL。
6. 保存更改。

### 选项 2：通过环境变量配置

您也可以通过设置 `WEBHOOK_URL` 环境变量来配置 webhook URL。有关 Open WebUI 中环境变量的更多信息，请参阅 [环境变量配置](https://docs.openwebui.com/getting-started/advanced-topics/env-configuration/#webhook_url)。

### 步骤 3：验证 Webhook

要验证 webhook 是否正常工作，请在 Open WebUI 中创建一个新用户账户。如果 webhook 配置正确，您应在指定的 webhook URL 处收到通知。

Webhook 负载格式
----------------------

由 Open WebUI 发送的 webhook 负载为纯文本，并包含关于新用户账户的简单通知消息。负载格式如下：

```
新用户已注册：<用户名>
```

例如，如果名为 "Tim" 的用户注册了，发送的负载将是：

```
新用户已注册：Tim
```

故障排除
--------------

* 确保 webhook URL 正确且格式无误。
* 验证 webhook 服务是否已启用并正确配置。
* 检查 Open WebUI 日志中是否有与 webhook 相关的错误。
* 确认连接未被防火墙或代理中断或阻止。
* Webhook 服务器可能暂时不可用或延迟较高。
* 如果通过 webhook 服务提供，请验证 Webhook API 密钥是否无效、过期或已被撤销。

注意：Open WebUI 中的 webhook 功能仍在不断发展中，我们计划在未来添加更多功能和事件类型。
