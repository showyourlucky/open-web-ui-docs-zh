---
sidebar_position: 11
title: "🖥️ 在 Intel GPU 上使用 IPEX-LLM 配置本地 LLM"
---

:::warning
此教程为社区贡献，未经 OpenWebUI 团队支持。它仅作为如何根据特定需求定制 OpenWebUI 的示例。想要贡献？请参阅贡献指南。
:::

:::note
本指南已通过 [手动安装](/getting-started/index.md) 的 Open WebUI 设置验证。
:::

# 在 Intel GPU 上使用 IPEX-LLM 配置本地 LLM

:::info
[**IPEX-LLM**](https://github.com/intel-analytics/ipex-llm) 是一个用于在 Intel CPU 和 GPU（例如带有集成显卡的本地 PC、Arc A 系列、Flex 和 Max 等独立显卡）上运行 LLM 的 PyTorch 库，具有极低的延迟。
:::

本教程展示了如何在 Intel GPU 上配置由 IPEX-LLM 加速的 Ollama 后端来运行 Open WebUI。按照本指南操作后，即使是在仅有集成显卡的低成本 PC 上，您也能享受流畅的体验。

## 在 Intel GPU 上启动 Ollama 服务

请参考 IPEX-LLM 官方文档中的[此指南](https://ipex-llm.readthedocs.io/en/latest/doc/LLM/Quickstart/ollama_quickstart.html)，了解如何在 Intel GPU 上安装并运行由 IPEX-LLM 加速的 Ollama 服务。

:::tip
如果您希望从另一台机器访问 Ollama 服务，请确保在执行 `ollama serve` 命令之前设置或导出环境变量 `OLLAMA_HOST=0.0.0.0`。
:::

## 配置 Open WebUI

通过菜单中的 **设置 -> 连接** 访问 Ollama 设置。默认情况下，**Ollama 基础 URL** 已预设为 `https://localhost:11434`，如下图所示。要验证与 Ollama 服务的连接状态，请点击文本框旁边的 **刷新按钮**。如果 WebUI 无法连接到 Ollama 服务器，您将看到一条错误消息：“WebUI 无法连接到 Ollama”。

![Open WebUI Ollama 设置失败](https://llm-assets.readthedocs.io/en/latest/_images/open_webui_settings_0.png)

如果连接成功，您将看到一条消息：“服务连接已验证”，如下图所示。

![Open WebUI Ollama 设置成功](https://llm-assets.readthedocs.io/en/latest/_images/open_webui_settings.png)

:::tip
如果您想使用托管在不同 URL 上的 Ollama 服务器，只需更新 **Ollama 基础 URL** 到新的 URL，并点击 **刷新** 按钮以重新确认与 Ollama 的连接。
:::
