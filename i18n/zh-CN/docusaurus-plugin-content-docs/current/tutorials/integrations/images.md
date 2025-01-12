---
sidebar_position: 6
title: "🎨 图像生成"
---

:::warning
此教程由社区贡献，并未得到 OpenWebUI 团队的支持。它仅作为如何根据您的具体需求自定义 OpenWebUI 的示例。想要贡献？请查看贡献教程。
:::

# 🎨 图像生成

Open WebUI 支持通过三种后端进行图像生成：**AUTOMATIC1111**、**ComfyUI** 和 **OpenAI DALL·E**。本指南将帮助您设置并使用这些选项之一。

## AUTOMATIC1111

Open WebUI 支持通过 **AUTOMATIC1111** [API](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/API) 进行图像生成。以下是开始使用的步骤：

### 初始设置

1. 确保已安装 [AUTOMATIC1111](https://github.com/AUTOMATIC1111/stable-diffusion-webui)。
2. 使用以下命令启动 AUTOMATIC1111 并启用 API 访问权限：

   ```bash
   ./webui.sh --api --listen
   ```

3. 如果您使用 Docker 安装 WebUI 并预设了环境变量，可以使用以下命令：

   ```bash
   docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -e AUTOMATIC1111_BASE_URL=http://host.docker.internal:7860/ -e ENABLE_IMAGE_GENERATION=True -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
   ```

### 在 Open WebUI 中设置 AUTOMATIC1111

1. 在 Open WebUI 中，导航到 **Admin Panel** > **Settings** > **Images** 菜单。
2. 将 `Image Generation Engine` 字段设置为 `Default (Automatic1111)`。
3. 在 API URL 字段中输入 AUTOMATIC1111 API 可访问的地址：

   ```bash
   http://<your_automatic1111_address>:7860/
   ```

   如果您在同一主机上运行 Docker 版本的 Open WebUI 和 AUTOMATIC1111，请使用 `http://host.docker.internal:7860/` 作为地址。

## ComfyUI

ComfyUI 提供了一个替代界面，用于管理和与图像生成模型交互。了解更多或从其 [GitHub 页面](https://github.com/comfyanonymous/ComfyUI)下载。以下是与您的其他工具一起运行 ComfyUI 的设置说明。

### 初始设置

1. 从 [GitHub](https://github.com/comfyanonymous/ComfyUI) 下载并解压 ComfyUI 软件包到您希望的目录。
2. 要启动 ComfyUI，请运行以下命令：

   ```bash
   python main.py
   ```

   对于低显存系统，使用以下命令以减少内存使用：

   ```bash
   python main.py --lowvram
   ```

3. 如果您使用 Docker 安装 WebUI 并预设了环境变量，可以使用以下命令：

   ```bash
   docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -e COMFYUI_BASE_URL=http://host.docker.internal:7860/ -e ENABLE_IMAGE_GENERATION=True -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
   ```

### 在 Open WebUI 中设置 ComfyUI

#### 设置 FLUX.1 模型

1. **模型检查点**：

* 从 [black-forest-labs HuggingFace 页面](https://huggingface.co/black-forest-labs)下载 `FLUX.1-schnell` 或 `FLUX.1-dev` 模型。
* 将模型检查点放置在 ComfyUI 的 `models/checkpoints` 和 `models/unet` 目录中。或者，您可以在这两个目录之间创建符号链接，以确保它们包含相同的模型检查点。

2. **VAE 模型**：

* 从 [这里](https://huggingface.co/black-forest-labs/FLUX.1-schnell/blob/main/ae.safetensors) 下载 `ae.safetensors` VAE。
* 将其放置在 ComfyUI 的 `models/vae` 目录中。

3. **CLIP 模型**：

* 从 [这里](https://huggingface.co/comfyanonymous/flux_text_encoders/tree/main) 下载 `clip_l.safetensors`。
* 将其放置在 ComfyUI 的 `models/clip` 目录中。

4. **T5XXL 模型**：

* 从 [这里](https://huggingface.co/comfyanonymous/flux_text_encoders/tree/main) 下载 `t5xxl_fp16.safetensors` 或 `t5xxl_fp8_e4m3fn.safetensors` 模型。
* 将其放置在 ComfyUI 的 `models/clip` 目录中。

要将 ComfyUI 集成到 Open WebUI 中，请按以下步骤操作：

#### 步骤 1：配置 Open WebUI 设置

1. 导航到 Open WebUI 中的 **Admin Panel**。
2. 单击 **Settings** 并选择 **Images** 标签页。
3. 在 `Image Generation Engine` 字段中选择 `ComfyUI`。
4. 在 **API URL** 字段中输入 ComfyUI API 可访问的地址，格式如下：`http://<your_comfyui_address>:8188/`。
   * 将环境变量 `COMFYUI_BASE_URL` 设置为此地址，以确保其在 WebUI 中持久化。

#### 步骤 2：验证连接并启用图像生成

1. 确保 ComfyUI 正在运行，并且已成功验证与 Open WebUI 的连接。没有成功连接则无法继续。
2. 连接验证成功后，打开 **Image Generation (Experimental)**。更多选项将会呈现给您。
3. 继续进行步骤 3 以完成最终配置步骤。

#### 步骤 3：配置 ComfyUI 设置并导入工作流

1. 在 ComfyUI 中启用开发者模式。找到 **Queue Prompt** 按钮上方的齿轮图标，并启用 `Dev Mode` 开关。
2. 使用 `Save (API Format)` 按钮将所需的工作流以 `API 格式` 导出。如果操作正确，文件将被下载为 `workflow_api.json`。
3. 返回 Open WebUI 并单击 **Click here to upload a workflow.json file** 按钮。
4. 选择 `workflow_api.json` 文件，将从 ComfyUI 导出的工作流导入到 Open WebUI 中。
5. 导入工作流后，必须根据导入的工作流节点 ID 映射 `ComfyUI Workflow Nodes`。
6. 将 `Set Default Model` 设置为您正在使用的模型文件名称，例如 `flux1-dev.safetensors`。

:::info
您可能需要调整 Open WebUI 的 `ComfyUI Workflow Nodes` 部分中的某些 `Input Key` 以匹配工作流中的节点。
例如，`seed` 可能需要重命名为 `noise_seed` 以匹配导入工作流中的节点 ID。
:::
:::tip
某些工作流，如使用 Flux 模型的工作流，可能会用到多个节点 ID，需要在 Open WebUI 的节点条目字段中填写。如果节点条目字段需要多个 ID，节点 ID 应该用逗号分隔（例如 `1` 或 `1, 2`）。
:::

6. 单击 `Save` 以应用设置，并享受与 ComfyUI 集成后的图像生成功能！

完成这些步骤后，您的 ComfyUI 设置应已集成到 Open WebUI 中，并且可以使用 Flux.1 模型进行图像生成。

### 使用 SwarmUI 进行配置

SwarmUI 使用 ComfyUI 作为其后端。为了让 Open WebUI 与 SwarmUI 兼容，您需要在 `ComfyUI Base URL` 后附加 `ComfyBackendDirect`。此外，您还需要设置具有局域网访问权限的 SwarmUI。上述调整完成后，设置 SwarmUI 与 Open WebUI 的步骤与 [步骤一：配置 Open WebUI 设置](https://github.com/open-webui/docs/edit/main/docs/features/images.md#step-1-configure-open-webui-settings) 中描述的相同。
![安装具有局域网访问权限的 SwarmUI](https://github.com/user-attachments/assets/a6567e13-1ced-4743-8d8e-be526207f9f6)

#### SwarmUI API URL

您将输入的 ComfyUI Base URL 格式如下：`http://<your_swarmui_address>:7801/ComfyBackendDirect`

## OpenAI DALL·E

Open WebUI 也支持通过 **OpenAI DALL·E APIs** 进行图像生成。此选项包括一个选择器，用于选择 DALL·E 2 或 DALL·E 3，每种模型支持不同的图像尺寸。

### 初始设置

1. 从 OpenAI 获取 [API 密钥](https://platform.openai.com/api-keys)。

### 配置 Open WebUI

1. 在 Open WebUI 中，导航到 **Admin Panel** > **Settings** > **Images** 菜单。
2. 将 `Image Generation Engine` 字段设置为 `Open AI (Dall-E)`。
3. 输入您的 OpenAI API 密钥。
4. 选择您希望使用的 DALL·E 模型。请注意，图像尺寸选项取决于所选模型：
   * **DALL·E 2**：支持 `256x256`、`512x512` 或 `1024x1024` 图像。
   * **DALL·E 3**：支持 `1024x1024`、`1792x1024` 或 `1024x1792` 图像。

### Azure OpenAI

直接使用 Azure OpenAI Dall-E 不受支持，但您可以设置一个 [LiteLLM 代理](https://litellm.vercel.app/docs/image_generation)，该代理与 `Open AI (Dall-E)` 图像生成引擎兼容。

## 使用图像生成

![图像生成教程](/img/tutorial_image_generation.png)

1. 首先，使用文本生成模型编写图像生成提示。
2. 响应完成后，可以点击图片图标生成图像。
3. 图像生成完成后，会自动返回并在聊天中显示。

:::tip
您也可以编辑 LLM 的响应并将图像生成提示作为消息发送，而不是使用 LLM 提供的实际响应。
:::
