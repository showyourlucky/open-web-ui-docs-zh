---
sidebar_position: 20
title: "💥 使用 Langfuse 进行监控与调试"
---

# Langfuse 与 OpenWebUI 的集成

[Langfuse](https://langfuse.com/) （[GitHub](https://github.com/langfuse/langfuse)）为 OpenWebUI 提供开源的可观测性和评估工具。通过启用 Langfuse 集成，您可以追踪应用程序数据，从而开发、监控并改进 OpenWebUI 的使用体验，包括：

- 应用程序 [跟踪](https://langfuse.com/docs/tracing)
- 使用模式
- 按用户和模型划分的成本数据
- 回放会话以调试问题
- [评估](https://langfuse.com/docs/scores/overview)

## 如何将 Langfuse 与 OpenWebUI 集成

![Langfuse 集成](https://langfuse.com/images/docs/openwebui-integration.gif)
_Langfuse 集成步骤_

OpenWebUI 中的 [Pipelines](https://github.com/open-webui/pipelines/) 是一个与 UI 无关的框架，用于 OpenAI API 插件。它允许注入插件，这些插件可以拦截、处理并将用户提示转发给最终的语言模型（LLM），从而增强对提示处理的控制和自定义。

要使用 Langfuse 跟踪您的应用程序数据，您可以使用 [Langfuse Pipeline](https://github.com/open-webui/pipelines/blob/d4fca4c37c4b8603be7797245e749e9086f35130/examples/filters/langfuse_filter_pipeline.py)，该管道支持实时监控和分析消息交互。

## 快速入门指南

### 第一步：设置 OpenWebUI

确保 OpenWebUI 已经运行。为此，请参阅 [OpenWebUI 文档](https://docs.openwebui.com/)。

### 第二步：设置 Pipelines

通过 Docker 启动 [Pipelines](https://github.com/open-webui/pipelines/)。使用以下命令启动 Pipelines：

```bash
docker run -p 9099:9099 --add-host=host.docker.internal:host-gateway -v pipelines:/app/pipelines --name pipelines --restart always ghcr.io/open-webui/pipelines:main
```

### 第三步：连接 OpenWebUI 和 Pipelines

在 _管理设置_ 中，创建并保存一个新的 OpenAI API 类型连接，并填写以下信息：

- **URL:** http://host.docker.internal:9099 （这是之前启动的 Docker 容器所在的地址）
- **密码:** 0p3n-w3bu! （默认密码）

![OpenWebUI 设置](https://langfuse.com/images/docs/openwebui-setup-settings.png)

### 第四步：添加 Langfuse Filter Pipeline

接下来，在 _管理设置_ -> _Pipelines_ 中添加 Langfuse Filter Pipeline。指定 Pipelines 监听的地址为 `http://host.docker.internal:9099`（如前所配置），并通过 _从 GitHub URL 安装_ 选项安装 [Langfuse Filter Pipeline](https://github.com/open-webui/pipelines/blob/main/examples/filters/langfuse_filter_pipeline.py)，使用以下 URL：

```
https://github.com/open-webui/pipelines/blob/main/examples/filters/langfuse_filter_pipeline.py
```

现在，在下方输入您的 Langfuse API 密钥。如果您尚未注册 Langfuse，可以通过 [这里](https://cloud.langfuse.com) 创建账户获取 API 密钥。

![OpenWebUI 添加 Langfuse Pipeline](https://langfuse.com//images/docs/openwebui-add-pipeline.png)

_**注意：** 在启用流式传输时捕获 OpenAI 模型的使用情况（令牌计数），您需要导航到 OpenWebUI 中的模型设置，并勾选“能力”下的“使用情况”[框](https://github.com/open-webui/open-webui/discussions/5770#discussioncomment-10778586)。_

### 第五步：在 Langfuse 中查看跟踪

现在，您可以与 OpenWebUI 应用程序进行交互，并在 Langfuse 中查看跟踪记录。

[示例跟踪](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/904a8c1f-4974-4f8f-8a2f-129ae78d99c5?observation=fe5b127b-e71c-45ab-8ee5-439d4c0edc28) 在 Langfuse 界面中：

![OpenWebUI 示例跟踪](https://langfuse.com/images/docs/openwebui-example-trace.png)

## 了解更多

有关 OpenWebUI Pipelines 的详细指南，请访问 [这篇文章](https://ikasten.io/2024/06/03/getting-started-with-openwebui-pipelines/)。
