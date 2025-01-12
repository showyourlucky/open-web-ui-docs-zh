---
sidebar_position: 13
title: "⚛️ Continue.dev VSCode 扩展与 Open WebUI 的集成"
---

:::warning
此教程由社区贡献，不受 OpenWebUI 团队支持。它仅作为如何根据特定需求定制 OpenWebUI 的示例。想要贡献？请查看贡献指南。
:::

# 集成 Continue.dev VSCode 扩展与 Open WebUI

### 下载扩展

你可以通过 [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=Continue.continue) 下载这个 VSCode 扩展。

安装完成后，你应该能在侧边栏中看到一个“continue”选项卡。

打开它，在右下角你会看到一个设置图标（看起来像一个齿轮）。

点击设置图标后，编辑器会打开一个 `config.json` 文件。

在这里，你可以配置 Continue 使用 Open WebUI。

---

目前，“ollama”提供者不支持身份验证，因此我们无法使用此提供者与 Open WebUI 配合。

然而，Ollama 和 Open WebUI 都兼容 OpenAI API 规范。你可以在 Ollama 的这篇博客文章中了解更多：[这里](https://ollama.com/blog/openai-compatibility)。

我们可以将 Continue 设置为使用 openai 提供者，这样就可以使用 Open WebUI 的身份验证令牌了。

---

## 配置

在 `config.json` 中，你需要做的只是添加或更改以下选项。

### 更改提供者为 openai

```json
"provider": "openai"
```

### 添加或更新 apiBase

将其设置为你的 Open Web UI 域名。

```json
"apiBase": "http://localhost:3000/" # 如果你按照 Docker 入门指南操作
```

### 添加 apiKey

```json
"apiKey": "sk-79970662256d425eb274fc4563d4525b" # 替换为你自己的 API 密钥
```

你可以在 Open WebUI -> 设置 -> 账户 -> API 密钥中找到并生成你的 API 密钥。
你需要复制“API Key”（以 sk- 开头的部分）。

## 示例配置

以下是一个使用 openai 提供者的 `config.json` 基础示例，使用 Granite Code 模型。确保你事先将模型导入到你的 ollama 实例中。

```json
{
  "models": [
    {
      "title": "Granite Code",
      "provider": "openai",
      "model": "granite-code:latest",
      "useLegacyCompletionsEndpoint": false,
      "apiBase": "http://YOUROPENWEBUI/ollama/v1",
      "apiKey": "sk-YOUR-API-KEY"
    }
  ],
  "customCommands": [
    {
      "name": "test",
      "prompt": "{{{ input }}}\n\n为选中的代码编写一套全面的单元测试。应包括设置、运行检查正确性的测试（包括重要的边界情况），以及清理。确保测试完整且复杂。仅以聊天输出的形式呈现测试，不要编辑任何文件。",
      "description": "为高亮的代码编写单元测试"
    }
  ],
  "tabAutocompleteModel": {
    "title": "Granite Code",
    "provider": "openai",
    "model": "granite-code:latest",
    "useLegacyCompletionsEndpoint": false,
    "apiBase": "http://localhost:3000/ollama/v1",
    "apiKey": "sk-YOUR-API-KEY"
  }
}
```

保存你的 `config.json` 文件即可！

你现在应该能在 Continue 选项卡的模型选择中看到你的模型了。

选择它，你现在应该可以通过 Open WebUI（以及你设置的任何 [管道](/pipelines)）进行对话。

你可以对任意数量的模型执行此操作，尽管任何模型都应能正常工作，但建议使用专为代码设计的模型。

更多关于 Continue 的配置，请参阅 [Continue 文档](https://docs.continue.dev/reference/Model%20Providers/openai)。