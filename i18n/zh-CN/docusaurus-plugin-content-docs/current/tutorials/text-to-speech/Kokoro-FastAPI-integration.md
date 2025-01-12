---
sidebar_position: 2
title: "🗨️ 使用 Docker 部署 Kokoro-FastAPI"
---

:::warning
此教程由社区贡献，并未得到 OpenWebUI 团队的支持。它仅作为如何根据特定需求自定义 OpenWebUI 的示例。想要贡献？请查看贡献指南。
:::

# 将 `Kokoro-FastAPI` 🗣️ 集成到 Open WebUI 中

## 什么是 `Kokoro-FastAPI`？

[Kokoro-FastAPI](https://github.com/remsky/Kokoro-FastAPI) 是一个基于 Docker 的 FastAPI 封装，用于 [Kokoro-82M](https://huggingface.co/hexgrad/Kokoro-82M) 文本转语音模型，并实现了 OpenAI API 端点规范。它提供了高性能的文本转语音功能，生成速度令人印象深刻：

- 通过 HF A100 实现超过 100 倍实时速度
- 通过 4060Ti 实现 35 到 50 倍实时速度
- 通过 M3 Pro CPU 实现超过 5 倍实时速度

主要特性：
- 兼容 OpenAI 的语音端点，支持内联语音组合
- NVIDIA GPU 加速或 CPU Onnx 推理
- 支持流式传输和可变分块
- 支持多种音频格式（mp3、wav、opus、flac、aac、pcm）
- 提供易于测试的 Web UI 界面
- 提供音素端点以进行转换和生成

声音选项：
 - af
 - af_bella
 - af_nicole
 - af_sarah
 - af_sky
 - am_adam
 - am_michael
 - bf_emma
 - bf_isabella
 - bf_george
 - bf_lewis

语言选项：
 - en_us
 - en_uk

## 要求

- 系统上已安装 Docker
- 运行中的 Open WebUI
- 对于 GPU 支持：带有 CUDA 12.1 的 NVIDIA GPU
- 仅使用 CPU：无需特殊要求

## ⚡️ 快速开始

您可以选择 GPU 或 CPU 版本：

```bash
# GPU 版本（需要带有 CUDA 12.1 的 NVIDIA GPU）
docker run -d -p 8880:8880 -p 7860:7860 remsky/kokoro-fastapi:latest

# CPU 版本（ONNX 优化推理）
docker run -d -p 8880:8880 -p 7860:7860 remsky/kokoro-fastapi:cpu-latest
```

## 设置 Open WebUI 以使用 `Kokoro-FastAPI`

- 打开管理面板并进入设置 -> 音频
- 将您的 TTS 设置调整为如下所示：
- - 文本转语音引擎：OpenAI
  - API 基础 URL：`http://localhost:8880/v1`
  - API 密钥：`not-needed`
  - TTS 模型：`kokoro`
  - TTS 声音：`af_bella`



:::info
默认的 API 密钥是字符串 `not-needed`。如果您不需要额外的安全性，则无需更改该值。
:::

## 构建 Docker 容器

```bash
git clone https://github.com/remsky/Kokoro-FastAPI.git
cd Kokoro-FastAPI
docker compose up --build
```

**就是这样！**

# 请参阅 [Kokoro-FastAPI](https://github.com/remsky/Kokoro-FastAPI) 仓库以获取构建 Docker 容器的说明（例如更改端口等）。
