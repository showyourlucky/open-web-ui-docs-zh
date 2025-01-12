---
sidebar_position: 16
title: "🌐 浏览器搜索引擎"
---

:::warning
此教程由社区贡献，并未得到 OpenWebUI 团队的支持。它仅作为如何根据您的具体需求自定义 OpenWebUI 的示例。想要贡献？请参阅贡献教程。
:::

# 浏览器搜索引擎集成

Open WebUI 可以直接集成到您的网络浏览器中。本教程将引导您完成将 Open WebUI 设置为自定义搜索引擎的过程，使您能够从浏览器的地址栏轻松执行查询。

## 将 Open WebUI 设置为搜索引擎

### 前提条件

在开始之前，请确保：

- 您已安装 Chrome 或其他支持的浏览器。
- `WEBUI_URL` 环境变量已正确设置，可以通过 Docker 环境变量或 `.env` 文件进行设置，具体方法请参阅[入门指南](/getting-started/advanced-topics/env-configuration)中的说明。

### 第一步：设置 WEBUI_URL 环境变量

设置 `WEBUI_URL` 环境变量可以确保浏览器知道将查询定向到哪里。

#### 使用 Docker 环境变量

如果您使用 Docker 运行 Open WebUI，可以在 `docker run` 命令中设置环境变量：

```bash
docker run -d \
  -p 3000:8080 \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  -e WEBUI_URL="https://<your-open-web-ui-url>" \
  ghcr.io/open-webui/open-webui:main
```

或者，您可以将变量添加到 `.env` 文件中：

```plaintext
WEBUI_URL=https://<your-open-web-ui-url>
```

### 第二步：添加 Open WebUI 作为自定义搜索引擎

### 对于 Chrome

1. 打开 Chrome 并导航至**设置**。
2. 在侧边栏中选择**搜索引擎**，然后点击**管理搜索引擎**。
3. 点击**添加**以创建新的搜索引擎。
4. 填写以下信息：
    - **搜索引擎**：Open WebUI Search
    - **关键词**：webui（或您喜欢的任何关键词）
    - **包含 %s 替换查询的 URL**：

      ```
      https://<your-open-web-ui-url>/?q=%s
      ```

5. 点击**添加**保存配置。

### 对于 Firefox

1. 在 Firefox 中访问 Open WebUI。
2. 点击地址栏以展开它。
3. 点击扩展地址栏底部绿色圆圈内的加号图标，这会将 Open WebUI 的搜索添加到您的偏好设置中的搜索引擎列表里。

或者：

1. 在 Firefox 中访问 Open WebUI。
2. 右键点击地址栏。
3. 从上下文菜单中选择“添加 Open WebUI”（或类似选项）。

### 可选：使用特定模型

如果您希望使用特定模型进行搜索，可以在 URL 格式中加入模型 ID：

```
https://<your-open-web-ui-url>/?models=<model_id>&q=%s
```

**注意**：模型 ID 需要进行 URL 编码。特殊字符如空格或斜杠需要编码（例如，“my model”应变为“my%20model”）。

## 示例用法

一旦搜索引擎设置完成，您就可以直接从地址栏进行搜索。只需输入您选择的关键词，然后跟上查询内容：

```
webui 您的查询内容
```

这条命令会将您重定向到 Open WebUI 界面，并显示您的搜索结果。

## 故障排除

如果遇到问题，请检查以下几点：

- 确保 `WEBUI_URL` 已正确配置并指向有效的 Open WebUI 实例。
- 再次确认浏览器设置中的搜索引擎 URL 格式是否正确。
- 确认您的互联网连接正常且 Open WebUI 服务运行顺畅。
