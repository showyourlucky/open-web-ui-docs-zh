---
sidebar_position: 5
title: "📜 Open WebUI 日志记录"
---

## 浏览器客户端日志记录 ##

客户端日志记录通常通过 [JavaScript](https://developer.mozilla.org/en-US/docs/Web/API/console/log_static) 的 `console.log()` 方法实现，可以通过内置的浏览器开发者工具进行访问：

* Blink
  * [Chrome/Chromium](https://developer.chrome.com/docs/devtools/)
  * [Edge](https://learn.microsoft.com/en-us/microsoft-edge/devtools-guide-chromium/overview)
* Gecko
  * [Firefox](https://firefox-source-docs.mozilla.org/devtools-user/)
* WebKit
  * [Safari](https://developer.apple.com/safari/tools/)

## 应用服务器/后端日志记录 ##

日志记录是一个持续进行的工作，但可以通过环境变量实现一定程度的控制。[Python 日志记录](https://docs.python.org/3/howto/logging.html) 使用 `log()` 和 `print()` 语句将信息发送到控制台。默认的日志级别是 `INFO`。理想情况下，敏感数据只会在 `DEBUG` 级别下暴露。

### 日志级别 ###

以下支持的日志级别及其对应的数值如下：

| 级别      | 数值    |
| ---------- | -------- |
| `CRITICAL` | 50       |
| `ERROR`    | 40       |
| `WARNING`  | 30       |
| `INFO`     | 20       |
| `DEBUG`    | 10       |
| `NOTSET`   | 0        |

### 全局设置 ###

默认的全局日志级别 `INFO` 可以通过环境变量 `GLOBAL_LOG_LEVEL` 进行覆盖。当设置此变量时，它会在 `config.py` 中执行一个带有 `force` 参数（设置为 *True*）的 [basicConfig](https://docs.python.org/3/library/logging.html#logging.basicConfig) 语句。这将重新配置所有附加的日志记录器：
> 如果指定了此关键字参数并设置为 true，则在根据其他参数进行配置之前，会移除并关闭根日志记录器上现有的所有处理器。

日志流使用标准输出 (`sys.stdout`)。除了所有 Open-WebUI 的 `log()` 语句外，这也会影响任何使用 Python 日志模块 `basicConfig` 机制的导入模块，包括 [urllib](https://docs.python.org/3/library/urllib.html)。

例如，要将 `DEBUG` 日志级别作为 Docker 参数设置，可以使用：

```bash
--env GLOBAL_LOG_LEVEL="DEBUG"
```

### 应用程序/后端 ###

可以通过以下任意组合的环境变量实现一定的细粒度控制。请注意，目前并未使用 `basicConfig` 的 `force` 参数，因此这些设置可能仅影响 Open-WebUI 的日志记录，而不会影响第三方模块。

| 环境变量            | 应用程序/后端                                                       |
| -------------------- | ------------------------------------------------------------------- |
| `AUDIO_LOG_LEVEL`    | 音频转录，使用 faster-whisper、TTS 等                              |
| `COMFYUI_LOG_LEVEL`  | ComfyUI 集成处理                                                   |
| `CONFIG_LOG_LEVEL`   | 配置处理                                                           |
| `DB_LOG_LEVEL`       | 内部 Peewee 数据库                                                 |
| `IMAGES_LOG_LEVEL`   | AUTOMATIC1111 稳定扩散图像生成                                      |
| `MAIN_LOG_LEVEL`     | 主（根）执行                                                       |
| `MODELS_LOG_LEVEL`   | LLM 模型交互、认证等                                               |
| `OLLAMA_LOG_LEVEL`   | Ollama 后端交互                                                    |
| `OPENAI_LOG_LEVEL`   | OpenAI 交互                                                        |
| `RAG_LOG_LEVEL`      | 使用 Chroma/Sentence-Transformers 的检索增强生成                   |
| `WEBHOOK_LOG_LEVEL`  | 认证 Webhook 扩展日志记录                                          |
