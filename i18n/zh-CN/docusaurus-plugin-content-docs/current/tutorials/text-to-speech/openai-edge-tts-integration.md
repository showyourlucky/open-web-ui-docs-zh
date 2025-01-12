---
sidebar_position: 1
title: "🗨️ 使用 Docker 集成 Edge TTS"
---

:::warning
本教程由社区贡献，未得到 OpenWebUI 团队的支持。它仅作为如何根据特定需求自定义 OpenWebUI 的示例。想要贡献？请查看贡献指南。
:::

# 将 `openai-edge-tts` 🗣️ 集成到 Open WebUI 中

## 什么是 `openai-edge-tts`，它与 `openedai-speech` 有何不同？

类似于 [openedai-speech](https://github.com/matatonic/openedai-speech)，[openai-edge-tts](https://github.com/travisvn/openai-edge-tts) 是一个模拟 OpenAI API 端点的文本转语音（TTS）API 端点，允许在可以调用 OpenAI 语音端点且服务器端点 URL 可配置的情况下直接替代使用。

`openedai-speech` 是一个更全面的选择，支持完全离线生成语音，并提供多种模式供选择。

`openai-edge-tts` 则是一个较为简单的选项，它使用名为 `edge-tts` 的 Python 包来生成音频。

`edge-tts` （[仓库](https://github.com/rany2/edge-tts)）利用 Edge 浏览器免费的“大声朗读”功能来模拟请求 Microsoft/Azure，从而免费获得高质量的文本转语音服务。

## 要求

- 系统中已安装 Docker
- 正在运行的 Open WebUI
- ffmpeg（可选 - 仅在不使用 `mp3` 格式时需要）

## ⚡️ 快速开始

最简单的开始方式是无需任何配置，只需运行以下命令：

```bash
docker run -d -p 5050:5050 travisvn/openai-edge-tts:latest
```

这将使用所有默认配置在端口 5050 上运行服务。

## 设置 Open WebUI 使用 `openai-edge-tts`

- 打开管理面板并进入设置 -> 音频
- 将您的 TTS 设置调整为与下方截图一致
- _注意：您可以在此指定 TTS 语音_

![Open WebUI 管理设置中的音频添加正确端点的截图](https://utfs.io/f/MMMHiQ1TQaBobmOhsMkrO6Tl2kxX39dbuFiQ8cAoNzysIt7f)

:::info
默认的 API 密钥是字符串 `your_api_key_here`。如果您不需要额外的安全性，则无需更改该值。
:::

**这样就完成了！您可以在这一阶段结束。**

参见[使用方法](#使用方法)部分获取请求示例。

# 如果您觉得 [OpenAI Edge TTS](https://github.com/travisvn/openai-edge-tts) 有用，请在 GitHub 上给该项目加个星 ⭐️

:::tip
您可以在 `docker run` 命令中直接定义环境变量。请参阅下面的 [Docker 快速配置](#-docker-快速配置)。
:::

## 替代方案

### 🐍 使用 Python 运行

如果您希望直接通过 Python 运行此项目，请按照以下步骤设置虚拟环境、安装依赖并启动服务器。

#### 1. 克隆仓库

```bash
git clone https://github.com/travisvn/openai-edge-tts.git
cd openai-edge-tts
```

#### 2. 设置虚拟环境

创建并激活虚拟环境以隔离依赖项：

```bash
# 对于 macOS/Linux
python3 -m venv venv
source venv/bin/activate

# 对于 Windows
python -m venv venv
venv\Scripts\activate
```

#### 3. 安装依赖

使用 `pip` 安装 `requirements.txt` 文件中列出的必要包：

```bash
pip install -r requirements.txt
```

#### 4. 配置环境变量

在根目录下创建一个 `.env` 文件，并设置以下变量：

```plaintext
API_KEY=your_api_key_here
PORT=5050

DEFAULT_VOICE=en-US-AndrewNeural
DEFAULT_RESPONSE_FORMAT=mp3
DEFAULT_SPEED=1.2

DEFAULT_LANGUAGE=en-US

REQUIRE_API_KEY=True
REMOVE_FILTER=False
EXPAND_API=True
```

#### 5. 启动服务器

配置完成后，使用以下命令启动服务器：

```bash
python app/server.py
```

服务器将在 `http://localhost:5050` 上运行。

#### 6. 测试 API

现在可以通过 `http://localhost:5050/v1/audio/speech` 和其他可用端点与 API 进行交互。参见[使用方法](#使用方法)部分获取请求示例。

#### 使用方法

##### 端点：`/v1/audio/speech`（别名 `/audio/speech`）

从输入文本生成音频。可用参数如下：

**必填参数：**

- **input**（字符串）：要转换为音频的文本（最多 4096 个字符）。

**可选参数：**

- **model**（字符串）：设置为 "tts-1" 或 "tts-1-hd"（默认："tts-1"）。
- **voice**（字符串）：OpenAI 兼容的语音之一（alloy, echo, fable, onyx, nova, shimmer）或任何有效的 `edge-tts` 语音（默认："en-US-AndrewNeural"）。
- **response_format**（字符串）：音频格式。选项：`mp3`, `opus`, `aac`, `flac`, `wav`, `pcm`（默认：`mp3`）。
- **speed**（数字）：播放速度（0.25 至 4.0）。默认值为 `1.2`。

:::tip
您可以在 [tts.travisvn.com](https://tts.travisvn.com) 浏览可用的语音并听取样本预览。
:::

使用 `curl` 发送请求并将输出保存为 mp3 文件的示例：

```bash
curl -X POST http://localhost:5050/v1/audio/speech \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your_api_key_here" \
  -d '{
    "input": "Hello, I am your AI assistant! Just let me know how I can help bring your ideas to life.",
    "voice": "echo",
    "response_format": "mp3",
    "speed": 1.0
  }' \
  --output speech.mp3
```

或者，符合 OpenAI API 端点参数：

```bash
curl -X POST http://localhost:5050/v1/audio/speech \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your_api_key_here" \
  -d '{
    "model": "tts-1",
    "input": "Hello, I am your AI assistant! Just let me know how I can help bring your ideas to life.",
    "voice": "alloy"
  }' \
  --output speech.mp3
```

另一种语言的示例：

```bash
curl -X POST http://localhost:5050/v1/audio/speech \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your_api_key_here" \
  -d '{
    "model": "tts-1",
    "input": "じゃあ、行く。電車の時間、調べておくよ。",
    "voice": "ja-JP-KeitaNeural"
  }' \
  --output speech.mp3
```

##### 其他端点

- **POST/GET /v1/models**：列出可用的 TTS 模型。
- **POST/GET /v1/voices**：列出给定语言/区域的 `edge-tts` 语音。
- **POST/GET /v1/voices/all**：列出所有 `edge-tts` 语音及其语言支持信息。

:::info
`/v1` 现在是可选的。

此外，还有针对 **Azure AI Speech** 和 **ElevenLabs** 的端点，如果这些选项在 Open WebUI 中允许自定义 API 端点，则未来可能提供支持。

这些可以通过设置环境变量 `EXPAND_API=False` 来禁用。
:::

## 🐳 Docker 快速配置

您可以在运行项目的命令中配置环境变量：

```bash
docker run -d -p 5050:5050 \
  -e API_KEY=your_api_key_here \
  -e PORT=5050 \
  -e DEFAULT_VOICE=en-US-AndrewNeural \
  -e DEFAULT_RESPONSE_FORMAT=mp3 \
  -e DEFAULT_SPEED=1.2 \
  -e DEFAULT_LANGUAGE=en-US \
  -e REQUIRE_API_KEY=True \
  -e REMOVE_FILTER=False \
  -e EXPAND_API=True \
  travisvn/openai-edge-tts:latest
```

:::note
Markdown 文本现在会经过过滤以增强可读性和支持。

您可以通过设置环境变量 `REMOVE_FILTER=True` 来禁用此功能。
:::

## 附加资源

有关 `openai-edge-tts` 的更多信息，请访问 [GitHub 仓库](https://github.com/travisvn/openai-edge-tts)

如需直接支持，请访问 [Voice AI & TTS Discord](https://tts.travisvn.com/discord)

## 🎙️ 语音样本

[播放语音样本并查看所有可用的 Edge TTS 语音](https://tts.travisvn.com/)
