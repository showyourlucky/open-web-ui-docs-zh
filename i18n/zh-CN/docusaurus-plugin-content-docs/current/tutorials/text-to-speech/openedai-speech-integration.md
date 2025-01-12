---
sidebar_position: 2
title: "🗨️ 使用 Docker 集成 Openedai-speech"
---

:::warning
此教程由社区贡献，OpenWebUI 团队不提供支持。它仅作为如何根据特定需求自定义 OpenWebUI 的示例。想要贡献？请查看贡献教程。
:::

**使用 Docker 将 `openedai-speech` 集成到 Open WebUI**
==============================================================

**什么是 `openedai-speech`？**
-----------------------------

:::info
[openedai-speech](https://github.com/matatonic/openedai-speech) 是一个与 OpenAI 音频/语音 API 兼容的文本转语音服务器。

它提供了 `/v1/audio/speech` 端点，并且可以免费、私密地进行文本转语音，支持自定义声音克隆功能。该服务与 OpenAI 没有任何关联，也不需要 OpenAI API 密钥。
:::

**要求**
-----------------

* 系统上已安装 Docker
* Open WebUI 运行在 Docker 容器中
* 基本了解 Docker 和 Docker Compose

**选项 1：使用 Docker Compose**
----------------------------------

**步骤 1：为 `openedai-speech` 服务创建新文件夹**
-----------------------------------------------------------------

创建一个新的文件夹，例如 `openedai-speech-service`，用于存储 `docker-compose.yml` 和 `speech.env` 文件。

**步骤 2：从 GitHub 克隆 `openedai-speech` 仓库**
--------------------------------------------------------------

```bash
git clone https://github.com/matatonic/openedai-speech.git
```

这将把 `openedai-speech` 仓库下载到你的本地机器上，其中包括 Docker Compose 文件（`docker-compose.yml`、`docker-compose.min.yml` 和 `docker-compose.rocm.yml`）及其他必要的文件。

**步骤 3：将 `sample.env` 文件重命名为 `speech.env`（如有需要可自定义）**
------------------------------------------------------------------------------

在 `openedai-speech` 仓库文件夹中，创建一个名为 `speech.env` 的新文件，内容如下：

```yaml
TTS_HOME=voices
HF_HOME=voices
#PRELOAD_MODEL=xtts
#PRELOAD_MODEL=xtts_v2.0.2
#PRELOAD_MODEL=parler-tts/parler_tts_mini_v0.1
#EXTRA_ARGS=--log-level DEBUG --unload-timer 300
#USE_ROCM=1
```

**步骤 4：选择一个 Docker Compose 文件**
----------------------------------------

你可以使用以下任意一个 Docker Compose 文件：

* [docker-compose.yml](https://github.com/matatonic/openedai-speech/blob/main/docker-compose.yml)：此文件使用 `ghcr.io/matatonic/openedai-speech` 镜像，并基于 [Dockerfile](https://github.com/matatonic/openedai-speech/blob/main/Dockerfile) 构建。
* [docker-compose.min.yml](https://github.com/matatonic/openedai-speech/blob/main/docker-compose.min.yml)：此文件使用 `ghcr.io/matatonic/openedai-speech-min` 镜像，并基于 [Dockerfile.min](https://github.com/matatonic/openedai-speech/blob/main/Dockerfile.min) 构建。这个镜像只包含 Piper 支持，不需要 GPU。
* [docker-compose.rocm.yml](https://github.com/matatonic/openedai-speech/blob/main/docker-compose.rocm.yml)：此文件使用 `ghcr.io/matatonic/openedai-speech-rocm` 镜像，并基于 [Dockerfile](https://github.com/matatonic/openedai-speech/blob/main/Dockerfile) 构建，支持 ROCm。

**步骤 4：构建选定的 Docker 镜像**
-----------------------------------------

在运行 Docker Compose 文件之前，你需要构建 Docker 镜像：

* **Nvidia GPU（CUDA 支持）**：

```bash
docker build -t ghcr.io/matatonic/openedai-speech .
```

* **AMD GPU（ROCm 支持）**：

```bash
docker build -f Dockerfile --build-arg USE_ROCM=1 -t ghcr.io/matatonic/openedai-speech-rocm .
```

* **仅 CPU，无 GPU（仅 Piper）**：

```bash
docker build -f Dockerfile.min -t ghcr.io/matatonic/openedai-speech-min .
```

**步骤 5：运行正确的 `docker compose up -d` 命令**
----------------------------------------------------------

* **Nvidia GPU（CUDA 支持）**：运行以下命令以在后台启动 `openedai-speech` 服务：

```bash
docker compose up -d
```

* **AMD GPU（ROCm 支持）**：运行以下命令以在后台启动 `openedai-speech` 服务：

```bash
docker compose -f docker-compose.rocm.yml up -d
```

* **ARM64（Apple M 系列，树莓派）**：XTTS 在这里只有 CPU 支持，速度会很慢。你可以使用 Nvidia 镜像并用 CPU 运行 XTTS（虽然很慢），或者使用仅 Piper 支持的镜像（推荐）：

```bash
docker compose -f docker-compose.min.yml up -d
```

* **仅 CPU，无 GPU（仅 Piper）**：对于仅有 Piper 支持的最小 Docker 镜像（小于 1GB 对比 8GB）：

```bash
docker compose -f docker-compose.min.yml up -d
```

这将在后台启动 `openedai-speech` 服务。

**选项 2：使用 Docker Run 命令**
---------------------------------------

你也可以使用以下 Docker run 命令在后台启动 `openedai-speech` 服务：

* **Nvidia GPU（CUDA）**：运行以下命令以构建并启动 `openedai-speech` 服务：

```bash
docker build -t ghcr.io/matatonic/openedai-speech .
docker run -d --gpus=all -p 8000:8000 -v voices:/app/voices -v config:/app/config --name openedai-speech ghcr.io/matatonic/openedai-speech
```

* **ROCm（AMD GPU）**：运行以下命令以构建并启动 `openedai-speech` 服务：

> 要启用 ROCm 支持，请取消注释 `speech.env` 文件中的 `#USE_ROCM=1` 行。

```bash
docker build -f Dockerfile --build-arg USE_ROCM=1 -t ghcr.io/matatonic/openedai-speech-rocm .
docker run -d --privileged --init --name openedai-speech -p 8000:8000 -v voices:/app/voices -v config:/app/config ghcr.io/matatonic/openedai-speech-rocm
```

* **仅 CPU，无 GPU（仅 Piper）**：运行以下命令以构建并启动 `openedai-speech` 服务：

```bash
docker build -f Dockerfile.min -t ghcr.io/matatonic/openedai-speech-min .
docker run -d -p 8000:8000 -v voices:/app/voices -v config:/app/config --name openedai-speech ghcr.io/matatonic/openedai-speech-min
```

**步骤 6：配置 Open WebUI 使用 `openedai-speech` 进行 TTS**
---------------------------------------------------------

![openedai-tts](https://github.com/silentoplayz/docs/assets/50341825/ea08494f-2ebf-41a2-bb0f-9b48dd3ace79)

打开 Open WebUI 设置并导航至 **Admin Panel > Settings > Audio** 下的 TTS 设置。添加以下配置：

* **API Base URL**: `http://host.docker.internal:8000/v1`
* **API Key**: `sk-111111111`（注意这是一个虚拟的 API 密钥，因为 `openedai-speech` 不需要 API 密钥。你可以在此字段中填写任何值，只要它是非空的即可。）

**步骤 7：选择一个声音**
--------------------------

在管理面板中的同一音频设置菜单下，你可以在 `TTS Voice` 中选择 `openedai-speech` 支持的模型之一。这些模型的声音经过优化，适用于英语。

* `tts-1` 或 `tts-1-hd`: `alloy`, `echo`, `echo-alt`, `fable`, `onyx`, `nova`, 和 `shimmer` (`tts-1-hd` 可配置，默认使用 OpenAI 样本)

**步骤 8：按 `Save` 应用更改并开始享受自然的声音**
--------------------------------------------------------------------------------------------

按下 `Save` 按钮应用对 Open WebUI 设置的更改。刷新页面以使更改完全生效，并开始享受集成 `openedai-speech` 后的 Open WebUI，使用文本转语音功能读取文本响应，生成自然的声音。

**模型详情**
------------------

`openedai-speech` 支持多种文本转语音模型，每个模型都有其独特的优势和要求。以下是可用的模型：

* **Piper TTS**（非常快，运行在 CPU 上）：通过 `voice_to_speaker.yaml` 配置文件使用自己的 [Piper 声音](https://rhasspy.github.io/piper-samples/)。这个模型非常适合需要低延迟和高性能的应用。Piper TTS 也支持 [多语言](https://github.com/matatonic/openedai-speech#multilingual) 声音。
* **Coqui AI/TTS XTTS v2**（快速，但需要大约 4GB GPU 显存及支持 CUDA 的 Nvidia GPU）：此模型使用 Coqui AI 的 XTTS v2 语音克隆技术生成高质量的声音。尽管它需要更强大的 GPU，但它提供了出色的表现和高质量的音频。Coqui 也支持 [多语言](https://github.com/matatonic/openedai-speech#multilingual) 声音。
* **Beta Parler-TTS 支持**（实验性，较慢）：此模型使用 Parler-TTS 框架生成声音。虽然目前处于测试阶段，但它允许你描述发言者声音的基本特征。每次生成的确切声音可能会有所不同，但应与提供的描述相似。关于如何描述声音，可以参考 [Text Description to Speech](https://www.text-description-to-speech.com/)。

**故障排除**
-------------------

如果你在将 `openedai-speech` 与 Open WebUI 集成时遇到问题，请遵循以下故障排除步骤：

* **验证 `openedai-speech` 服务**：确保 `openedai-speech` 服务正在运行，并且在 `docker-compose.yml` 文件中指定的端口已暴露。
* **检查对 `host.docker.internal` 的访问权限**：验证 `host.docker.internal` 主机名是否可以从 Open WebUI 容器中解析。这是因为 `openedai-speech` 通过 `localhost` 暴露在你的电脑上，而 `open-webui` 通常无法从容器内部访问它。你可以将一个卷添加到 `docker-compose.yml` 文件中，以便从主机挂载文件到容器，例如挂载到 `openedai-speech` 提供服务的目录。
* **审查 API 密钥配置**：确保 API 密钥设置为虚拟值或实际上未启用，因为 `openedai-speech` 不需要 API 密钥。
* **检查声音配置**：确认你在 TTS 中尝试使用的语音存在于 `voice_to_speaker.yaml` 文件中，并且相应的文件（如语音 XML 文件）位于正确目录中。
* **验证声音模型路径**：如果遇到声音模型加载问题，请仔细检查 `voice_to_speaker.yaml` 文件中的路径是否与实际的语音模型位置匹配。

**额外的故障排除提示**
------------------------------------

* 检查 `openedai-speech` 日志中的错误或警告信息，可能指示问题所在。
* 确认 `docker-compose.yml` 文件针对你的环境进行了正确配置。
* 如果仍然遇到问题，尝试重启 `openedai-speech` 服务或整个 Docker 环境。
* 如果问题依旧存在，请参阅 `openedai-speech` 的 GitHub 仓库或在相关社区论坛寻求帮助。

**常见问题解答**
-------

**如何控制生成音频的情感范围？**

没有直接机制来控制生成音频的情感输出。某些因素，如大小写或语法，可能会影响输出音频，但内部测试结果并不一致。

**语音文件存储在哪里？配置文件呢？**

定义可用声音及其属性的配置文件存储在 config 卷中。具体来说，默认声音定义在 `voice_to_speaker.default.yaml` 文件中。

**附加资源**
------------------------

有关如何配置 Open WebUI 以使用 `openedai-speech` 的更多信息，包括设置环境变量，请参阅 [Open WebUI 文档](https://docs.openwebui.com/getting-started/advanced-topics/env-configuration#text-to-speech)。

有关 `openedai-speech` 的更多信息，请访问 [GitHub 仓库](https://github.com/matatonic/openedai-speech)。

**如何向 openedai-speech 添加更多声音：**
[Custom-Voices-HowTo](https://github.com/matatonic/openedai-speech?tab=readme-ov-file#custom-voices-howto)

:::note
你可以将 `docker-compose.yml` 文件中的端口号更改为任何开放且可用的端口，但务必相应地更新 Open WebUI Admin Audio 设置中的 **API Base URL**。
:::
