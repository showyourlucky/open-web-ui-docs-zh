---
sidebar_position: 1
title: "🚰 过滤器"
---

# 过滤器

过滤器用于对用户输入的消息和助手（LLM）输出的消息执行各种操作。过滤器可以执行的操作包括将消息发送到监控平台（如 Langfuse 或 DataDog）、修改消息内容、阻止有害信息、翻译消息为其他语言，或限制某些用户的发送频率。在 [Pipelines 仓库](https://github.com/open-webui/pipelines/tree/main/examples/filters) 中维护了一份示例列表。过滤器可以在 Function 或 Pipelines 服务器上执行。整体工作流程如下图所示。

<p align="center">
  <a href="#">
    <img src="/img/pipelines/filters.png" alt="过滤器工作流程" />
  </a>
</p>

当某个模型或管道启用了过滤器管道时，用户输入的消息（或“入口”）会被传递给过滤器进行处理。过滤器会在请求 LLM 模型生成回复之前，先对消息执行所需的操作。最后，在消息发送给用户之前，过滤器还会对 LLM 输出的消息（或“出口”）进行后处理。
