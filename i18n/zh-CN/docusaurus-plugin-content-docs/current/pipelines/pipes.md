---
sidebar_position: 2
title: "🔧 管道"
---

# 管道

管道是一类函数，可以在将大型语言模型（LLM）的消息返回给用户之前执行某些操作。你可以通过管道实现的操作包括：检索增强生成（RAG）、向非 OpenAI 的 LLM 提供商（如 Anthropic、Azure OpenAI 或 Google）发送请求，或者直接在你的 Web UI 中执行功能。管道可以托管为一个函数或在一个管道服务器上运行。管道的示例列表可以在 [Pipelines 仓库](https://github.com/open-webui/pipelines/tree/main/examples/pipelines)中找到。一般的工作流程可以参考下图。


<p align="center">
  <a href="#">
    <img src="/img/pipelines/pipes.png" alt="管道工作流" />
  </a>
</p>


在你的 WebUI 中定义的管道会显示为带有“外部”标识的新模型。以下是两个管道模型的示例：“数据库 RAG 管道”和“DOOM”，它们与两个自托管模型一起展示如下：


<p align="center">
  <a href="#">
    <img src="/img/pipelines/pipe-model-example.png" alt="WebUI 中的管道模型" />
  </a>
</p>
