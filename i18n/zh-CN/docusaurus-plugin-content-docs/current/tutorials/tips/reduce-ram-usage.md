---
sidebar_position: 10
title: "✂️ 减少内存占用"
---

# 减少内存占用

如果你在一个内存受限的环境中部署这个镜像，可以采取一些措施来精简镜像。

在 Raspberry Pi 4 (arm64) 上使用 v0.3.10 版本时，这能够将空闲内存消耗从超过 1GB 减少到约 200MB（通过 `docker container stats` 观察）。

## 简要说明

设置以下环境变量（或现有部署中的相应 UI 设置）：`RAG_EMBEDDING_ENGINE: ollama`，`AUDIO_STT_ENGINE: openai`。

## 详细解释

大部分的内存消耗是由于加载了机器学习模型。即使你使用的是外部语言模型（OpenAI 或 unbundled ollama），也可能为了其他目的加载多个模型。

截至 v0.3.10 版本，包括：

* 语音转文字（默认为 whisper）
* RAG 嵌入引擎（默认使用本地 SentenceTransformers 模型）
* 图像生成引擎（默认禁用）

前两个选项默认启用并使用本地模型。你可以通过管理面板更改这些模型（RAG：文档类别，设置为 Ollama 或 OpenAI；语音转文字：音频部分，选择 OpenAI 或 WebAPI）。如果你正在部署一个新的 Docker 镜像，也可以通过以下环境变量进行设置：`RAG_EMBEDDING_ENGINE: ollama`，`AUDIO_STT_ENGINE: openai`。请注意，如果已经存在 `config.json` 文件，这些环境变量将不会生效。
