---
sidebar_position: 4
title: "🌍 环境变量配置"
---

## 概述

Open WebUI 提供了大量的环境变量，允许您自定义和配置应用程序的各个方面。本页作为所有可用环境变量的全面参考，提供其类型、默认值和描述。随着新变量的引入，本页将不断更新以反映不断增长的配置选项。

:::info

本页已与 Open WebUI 发布版本 [v0.5.1](https://github.com/open-webui/open-webui/releases/tag/v0.5.1) 保持同步，但仍在进行中，以便稍后包含更准确的描述，列出环境变量的可用选项、默认值并改进描述。

:::

### 关于 PersistentConfig 环境变量的重要说明

:::note

首次启动 Open WebUI 时，所有环境变量一视同仁，可以用于配置应用程序。然而，对于标记为 `PersistentConfig` 的环境变量，其值将被持久化并在内部存储。

在最初启动后，如果您重新启动容器，`PersistentConfig` 环境变量将不再使用外部环境变量值，而是使用内部存储的值。

相反，常规环境变量将在每次后续重启时继续更新和应用。

您可以直接从 Open WebUI 内部更新 `PersistentConfig` 环境变量的值，这些更改将被内部存储。这使您可以独立于外部环境变量管理这些配置设置。

请注意，`PersistentConfig` 环境变量在下面的文档中被清楚地标记为此，以便您了解它们的行为。

:::

## 应用程序/后端

以下环境变量由 `backend/config.py` 使用，以提供 Open WebUI 启动配置。请注意，根据您是直接运行 Open WebUI 还是通过 Docker 运行，某些变量可能具有不同的默认值。有关日志记录环境变量的详细信息，请参阅我们的[日志记录文档](/getting-started/advanced-topics/logging)。

### 通用

#### `ENV`

- 类型: `str` (枚举: `dev`, `prod`)
- 选项:
  - `dev` - 启用 `/docs` 上的 FastAPI API 文档
  - `prod` - 自动配置多个环境变量
- 默认值:
  - **后端默认**: `dev`
  - **Docker 默认**: `prod`
- 描述: 环境设置。

#### `CUSTOM_NAME`

- 类型: `str`
- 描述: 设置 `WEBUI_NAME`，但会轮询 **api.openwebui.com** 以获取元数据。

#### `WEBUI_NAME`

- 类型: `str`
- 默认值: `Open WebUI`
- 描述: 设置主 WebUI 名称。如果被覆盖，会追加 `(Open WebUI)`。

#### `WEBUI_URL`

- 类型: `str`
- 默认值: `http://localhost:3000`
- 描述: 指定 Open WebUI 可访问的 URL。目前用于搜索引擎支持。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `PORT`

- 类型: `int`
- 默认值: `8080`
- 描述: 设置运行 Open WebUI 的端口。

:::info

如果通过 Python 安装，您必须通过命令行参数传递 `--port`。

:::

#### `ENABLE_SIGNUP`

- 类型: `bool`
- 默认值: `True`
- 描述: 切换用户账户创建功能。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `ENABLE_LOGIN_FORM`

- 类型: `bool`
- 默认值: `True`
- 描述: 切换电子邮件、密码、登录和“或”（仅当 `ENABLE_OAUTH_SIGNUP` 设置为 True 时）元素。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

:::danger

当 [ENABLE_OAUTH_SIGNUP](https://docs.openwebui.com/getting-started/advanced-topics/env-configuration/#enable_oauth_signup) 也被使用并设置为 `True` 时，应该**仅**将此设置为 `False`。否则将无法登录。

:::

#### `ENABLE_REALTIME_CHAT_SAVE`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用后，系统将实时将每个流式聊天数据块保存到数据库中，以确保最大的数据持久性。此功能提供强大的数据恢复能力，并允许准确的会话跟踪。然而，代价是增加了延迟，因为保存到数据库会引入延迟。禁用此功能可以提高性能并减少延迟，但在系统故障或崩溃的情况下可能会导致数据丢失。根据应用程序的需求和可接受的权衡来使用。

#### `ENABLE_ADMIN_EXPORT`

- 类型: `bool`
- 默认值: `True`
- 描述: 控制管理员用户是否可以导出数据。

#### `ENABLE_ADMIN_CHAT_ACCESS`

- 类型: `bool`
- 默认值: `True`
- 描述: 允许管理员用户访问所有聊天。

#### `ENABLE_CHANNELS`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用或禁用频道支持。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `ADMIN_EMAIL`

- 类型: `str`
- 描述: 设置由 `SHOW_ADMIN_DETAILS` 显示的管理员电子邮件
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `SHOW_ADMIN_DETAILS`

- 类型: `bool`
- 默认值: `True`
- 描述: 切换是否在界面中显示管理员用户详细信息。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `BYPASS_MODEL_ACCESS_CONTROL`

- 类型: `bool`
- 默认值: `False`
- 描述: 绕过模型访问控制。

#### `DEFAULT_MODELS`

- 类型: `str`
- 默认值: 空字符串 (' ')，因为 `None` 作为默认值
- 描述: 设置默认的语言模型。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `DEFAULT_USER_ROLE`

- 类型: `str` (枚举: `pending`, `user`, `admin`)
- 选项:
  - `pending` - 新用户处于待定状态，直到管理员手动激活其账户。
  - `user` - 新用户自动激活，具有常规用户权限。
  - `admin` - 新用户自动激活，具有管理员权限。
- 默认值: `pending`
- 描述: 设置分配给新用户的默认角色。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `DEFAULT_LOCALE`

- 类型: `str`
- 默认值: `en`
- 描述: 设置应用程序的默认语言环境。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `WEBHOOK_URL`

- 类型: `str`
- 描述: 设置用于集成 Discord/Slack/Microsoft Teams 的 webhook。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `WEBUI_BUILD_HASH`

- 类型: `str`
- 默认值: `dev-build`
- 描述: 用于识别发布版本构建的 Git SHA。

#### `WEBUI_BANNERS`

- 类型: `list` of `dict`
- 默认值: `[]`
- 描述: 向用户显示的横幅列表。横幅的格式为:

```json
[{"id": "string","type": "string [info, success, warning, error]","title": "string","content": "string","dismissible": False,"timestamp": 1000}]
```

- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

:::info

在 `.env` 文件中设置此环境变量时，请确保通过将整个值用双引号括起来，并对内部引号使用转义引号 (`\"`) 来转义引号。例如:

```
WEBUI_BANNERS="[{\"id\": \"1\", \"type\": \"warning\", \"title\": \"Your messages are stored.\", \"content\": \"Your messages are stored and may be reviewed by human people. LLM's are prone to hallucinations, check sources.\", \"dismissible\": true, \"timestamp\": 1000}]"
```

:::

#### `JWT_EXPIRES_IN`

- 类型: `int`
- 默认值: `-1`
- 描述: 设置 JWT 过期时间（以秒为单位）。有效时间单位：`s`、`m`、`h`、`d`、`w` 或 `-1` 表示不过期。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `USE_CUDA_DOCKER`

- 类型: `bool`
- 默认值: `False`
- 描述: 构建具有 NVIDIA CUDA 支持的 Docker 镜像。启用本地 Whisper 和嵌入的 GPU 加速。

### AIOHTTP 客户端

#### `AIOHTTP_CLIENT_TIMEOUT`

- 类型: `int`
- 默认值: `300`
- 描述: 指定 aiohttp 客户端的超时时间（以秒为单位）。这会影响与 Ollama 和 OpenAI 端点的连接等。

:::info

这是客户端在超时之前等待响应的最大时间。如果设置为空字符串 (' ')，超时将设置为 `None`，有效地禁用超时并允许客户端无限期等待。

:::

#### `AIOHTTP_CLIENT_TIMEOUT_OPENAI_MODEL_LIST`

- 类型: `int`
- 描述: 设置获取 OpenAI 模型列表的超时时间（以秒为单位）。在网络延迟需要更长超时才能成功检索模型列表的情况下，这可能很有用。

### 目录

#### `DATA_DIR`

- 类型: `str`
- 默认值: `./data`
- 描述: 指定数据存储的基本目录，包括上传、缓存、向量数据库等。

#### `FONTS_DIR`

- 类型: `str`
- 描述: 指定字体的目录。

#### `FRONTEND_BUILD_DIR`

- 类型: `str`
- 默认值: `../build`
- 描述: 指定构建的前端文件的位置。

#### `STATIC_DIR`

- 类型: `str`
- 默认值: `./static`
- 描述: 指定静态文件的目录，例如图标。

### Ollama

#### `ENABLE_OLLAMA_API`

- 类型: `bool`
- 默认值: `True`
- 描述: 启用 Ollama API 的使用。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OLLAMA_BASE_URL` (`OLLAMA_API_BASE_URL` 已弃用) {#ollama_base_url}

- 类型: `str`
- 默认值: `http://localhost:11434`
- Docker 默认值:
  - 如果设置了 `K8S_FLAG`：`http://ollama-service.open-webui.svc.cluster.local:11434`
  - 如果 `USE_OLLAMA_DOCKER=True`：`http://localhost:11434`
  - 否则 `http://host.docker.internal:11434`
- 描述: 配置 Ollama 后端 URL。

#### `OLLAMA_BASE_URLS`

- 类型: `str`
- 描述: 配置负载均衡的 Ollama 后端主机，以 `;` 分隔。参见[`OLLAMA_BASE_URL`](#ollama_base_url)。优先于[`OLLAMA_BASE_URL`](#ollama_base_url)。
- 示例: `http://host-one:11434;http://host-two:11434`
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `USE_OLLAMA_DOCKER`

- 类型: `bool`
- 默认值: `False`
- 描述: 构建包含捆绑的 Ollama 实例的 Docker 镜像。

#### `K8S_FLAG`

- 类型: `bool`
- 默认值: `False`
- 描述: 如果设置，假定 Helm 图表部署并将 [`OLLAMA_BASE_URL`](#ollama_base_url) 设置为 `http://ollama-service.open-webui.svc.cluster.local:11434`

### OpenAI

#### `ENABLE_OPENAI_API`

- 类型: `bool`
- 默认值: `True`
- 描述: 启用 OpenAI API 的使用。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OPENAI_API_BASE_URL`

- 类型: `str`
- 默认值: `https://api.openai.com/v1`
- 描述: 配置 OpenAI 基础 API URL。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OPENAI_API_BASE_URLS`

- 类型: `str`
- 描述: 支持均衡的 OpenAI 基础 API URL，以分号分隔。
- 示例: `http://host-one:11434;http://host-two:11434`
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OPENAI_API_KEY`

- 类型: `str`
- 描述: 设置 OpenAI API 密钥。
- 示例: `sk-124781258123`
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OPENAI_API_KEYS`

- 类型: `str`
- 描述: 支持多个 OpenAI API 密钥，以分号分隔。
- 示例: `sk-124781258123;sk-4389759834759834`
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

### 任务

#### `TASK_MODEL`

- 类型: `str`
- 描述: 默认用于任务（如标题和网络搜索查询生成）的模型
当使用 Ollama 模型时。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `TASK_MODEL_EXTERNAL`

- 类型: `str`
- 描述: 默认用于任务（如标题和网络搜索查询生成）的模型
当使用 OpenAI 兼容端点时。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `TITLE_GENERATION_PROMPT_TEMPLATE`

- 类型: `str`
- 描述: 生成聊天标题时使用的提示。
- 默认值: `DEFAULT_TITLE_GENERATION_PROMPT_TEMPLATE` 环境变量的值。

模板:

```
Create a concise, 3-5 word title with an emoji as a title for the chat history, in the given language. Suitable Emojis for the summary can be used to enhance understanding but avoid quotation marks or special formatting. RESPOND ONLY WITH THE TITLE TEXT.

Examples of titles:
📉 Stock Market Trends
🍪 Perfect Chocolate Chip Recipe
Evolution of Music Streaming
Remote Work Productivity Tips
Artificial Intelligence in Healthcare
🎮 Video Game Development Insights

<chat_history>
{{MESSAGES:END:2}}
```

- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `TOOLS_FUNCTION_CALLING_PROMPT_TEMPLATE`

- 类型: `str`
- 描述: 调用工具时使用的提示。
- 默认值: `DEFAULT_TOOLS_FUNCTION_CALLING_PROMPT_TEMPLATE` 环境变量的值。

模板:

```
Available Tools: {{TOOLS}}\nReturn an empty string if no tools match the query. If a function tool matches, construct and return a JSON object in the format {\"name\": \"functionName\", \"parameters\": {\"requiredFunctionParamKey\": \"requiredFunctionParamValue\"}} using the appropriate tool and its parameters. Only return the object and limit the response to the JSON object without additional text.
```

- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

### 自动补全

#### `ENABLE_AUTOCOMPLETE_GENERATION`

- 类型: `bool`
- 默认值: `True`
- 描述: 启用或禁用自动补全生成。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

:::info

启用 `ENABLE_AUTOCOMPLETE_GENERATION` 时，请确保相应配置 `AUTOCOMPLETE_GENERATION_INPUT_MAX_LENGTH` 和 `AUTOCOMPLETE_GENERATION_PROMPT_TEMPLATE`。

:::

#### `AUTOCOMPLETE_GENERATION_INPUT_MAX_LENGTH`

- 类型: `int`
- 默认值: `-1`
- 描述: 设置自动补全生成的最大输入长度。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `AUTOCOMPLETE_GENERATION_PROMPT_TEMPLATE`

- 类型: `str`
- 默认值: `DEFAULT_AUTOCOMPLETE_GENERATION_PROMPT_TEMPLATE` 环境变量的值。

模板:

```
### Task:
You are an autocompletion system. Continue the text in `<text>` based on the **completion type** in `<type>` and the given language.  

### **Instructions**:
1. Analyze `<text>` for context and meaning.  
2. Use `<type>` to guide your output:  
   - **General**: Provide a natural, concise continuation.  
   - **Search Query**: Complete as if generating a realistic search query.  
3. Start as if you are directly continuing `<text>`. Do **not** repeat, paraphrase, or respond as a model. Simply complete the text.  
4. Ensure the continuation:
   - Flows naturally from `<text>`.  
   - Avoids repetition, overexplaining, or unrelated ideas.  
5. If unsure, return: `{ "text": "" }`.  

### **Output Rules**:
- Respond only in JSON format: `{ "text": "<your_completion>" }`.

### **Examples**:
#### Example 1:  
Input:  
<type>General</type>  
<text>The sun was setting over the horizon, painting the sky</text>  
Output:  
{ "text": "with vibrant shades of orange and pink." }

#### Example 2:  
Input:  
<type>Search Query</type>  
<text>Top-rated restaurants in</text>  
Output:  
{ "text": "New York City for Italian cuisine." }  

---
### Context:
<chat_history>
{{MESSAGES:END:6}}
</chat_history>
<type>{{TYPE}}</type>  
<text>{{PROMPT}}</text>  
#### Output:
```

- 描述: 设置自动补全生成的提示模板。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

### 评估竞技场模型

#### `ENABLE_EVALUATION_ARENA_MODELS`

- 类型: `bool`
- 默认值: `True`
- 描述: 启用或禁用评估竞技场模型。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `ENABLE_MESSAGE_RATING`

- 类型: `bool`
- 默认值: `True`
- 描述: 启用消息评级功能。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `ENABLE_COMMUNITY_SHARING`

- 类型: `bool`
- 默认值: `True`
- 描述: 控制用户是否可以看到分享至社区的按钮。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

### 标签生成

#### `ENABLE_TAGS_GENERATION`

- 类型: `bool`
- 默认值: `True`
- 描述: 启用或禁用标签生成。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `TAGS_GENERATION_PROMPT_TEMPLATE`

- 类型: `str`
- 默认值: `DEFAULT_TAGS_GENERATION_PROMPT_TEMPLATE` 环境变量的值。

模板:

```
### Task:
Generate 1-3 broad tags categorizing the main themes of the chat history, along with 1-3 more specific subtopic tags.

### Guidelines:
- Start with high-level domains (e.g. Science, Technology, Philosophy, Arts, Politics, Business, Health, Sports, Entertainment, Education)
- Consider including relevant subfields/subdomains if they are strongly represented throughout the conversation
- If content is too short (less than 3 messages) or too diverse, use only ["General"]
- Use the chat's primary language; default to English if multilingual
- Prioritize accuracy over specificity

### Output:
JSON format: { "tags": ["tag1", "tag2", "tag3"] }

### Chat History:
<chat_history>
{{MESSAGES:END:6}}
</chat_history>
```

- 描述: 设置标签生成提示模板。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

### API 密钥端点限制

#### `ENABLE_API_KEY_ENDPOINT_RESTRICTIONS`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用 API 密钥端点限制以增强安全性和可配置性。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `API_KEY_ALLOWED_ENDPOINTS`

- 类型: `str`
- 描述: 指定启用 API 密钥端点限制时允许的 API 端点的逗号分隔列表。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

:::note

`API_KEY_ALLOWED_ENDPOINTS` 的值应为端点 URL 的逗号分隔列表，例如 `/api/v1/messages, /api/v1/channels`。

:::

## 安全变量

#### `ENABLE_FORWARD_USER_INFO_HEADERS`

- 类型: `bool`
- 默认值: `False`
- 描述: 将用户信息（姓名、id、电子邮件和角色）作为 X-headers 转发到 OpenAI API。
如果启用，以下标头将被转发：
  - `X-OpenWebUI-User-Name`
  - `X-OpenWebUI-User-Id`
  - `X-OpenWebUI-User-Email`
  - `X-OpenWebUI-User-Role`

#### `ENABLE_RAG_LOCAL_WEB_FETCH`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用本地 Web 获取以用于 RAG。启用此选项允许针对本地网络资源的服务器端请求伪造攻击。

#### `ENABLE_RAG_WEB_LOADER_SSL_VERIFICATION`

- 类型: `bool`
- 默认值: `True`
- 描述: 绕过网站 RAG 的 SSL 验证。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `WEBUI_SESSION_COOKIE_SAME_SITE`

- 类型: `str` (枚举: `lax`, `strict`, `none`)
- 选项:
  - `lax` - 将 `SameSite` 属性设置为 lax，允许会话 cookie 随第三方网站发起的请求一起发送。
  - `strict` - 将 `SameSite` 属性设置为 strict，阻止会话 cookie 随第三方网站发起的请求一起发送。
  - `none` - 将 `SameSite` 属性设置为 none，允许会话 cookie 随第三方网站发起的请求一起发送，但仅限于 HTTPS。
- 默认值: `lax`
- 描述: 设置会话 cookie 的 `SameSite` 属性。

#### `WEBUI_SESSION_COOKIE_SECURE`

- 类型: `bool`
- 默认值: `False`
- 描述: 如果设置为 `True`，则为会话 cookie 设置 `Secure` 属性。

#### `WEBUI_AUTH`

- 类型: `bool`
- 默认值: `True`
- 描述: 此设置启用或禁用身份验证。

:::danger

如果设置为 `False`，则您的 Open WebUI 实例将禁用身份验证。但是，请注意，关闭身份验证仅适用于没有现有用户的新安装。如果已经有用户注册，您无法直接禁用身份验证。如果打算关闭 `WEBUI_AUTH`，请确保数据库中没有用户存在。

:::

#### `WEBUI_SECRET_KEY`

- 类型: `str`
- 默认值: `t0p-s3cr3t`
- Docker 默认值: 首次启动时随机生成
- 描述: 覆盖用于 JSON Web Token 的随机生成字符串。

#### `OFFLINE_MODE`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用或禁用离线模式。

#### `RESET_CONFIG_ON_START`

- 类型: `bool`
- 默认值: `False`
- 描述: 在启动时重置 `config.json` 文件。

#### `SAFE_MODE`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用安全模式，它禁用潜在的不安全功能，停用所有功能。

#### `CORS_ALLOW_ORIGIN`

- 类型: `str`
- 默认值: `*`
- 描述: 设置允许的跨源资源共享（CORS）来源。

#### `RAG_EMBEDDING_MODEL_TRUST_REMOTE_CODE`

- 类型: `bool`
- 默认值: `False`
- 描述: 决定是否允许在 Hub 上定义的自定义模型使用自己的建模文件。

#### `RAG_RERANKING_MODEL_TRUST_REMOTE_CODE`

- 类型: `bool`
- 默认值: `False`
- 描述: 决定是否允许在 Hub 上定义的自定义模型使用自己的建模文件进行重新排序。

#### `RAG_EMBEDDING_MODEL_AUTO_UPDATE`

- 类型: `bool`
- 默认值: `True`
- 描述: 切换 Sentence-Transformer 模型的自动更新。

#### `RAG_RERANKING_MODEL_AUTO_UPDATE`

- 类型: `bool`
- 默认值: `True`
- 描述: 切换重新排序模型的自动更新。

#### `WHISPER_MODEL_AUTO_UPDATE`

- 类型: `bool`
- 默认值: `False`
- 描述: 切换 Whisper 模型的自动更新。

## 检索增强生成（RAG）

#### `VECTOR_DB`

- 类型: `str`
- 选项:
- `chroma`, `milvus`, `qdrant`, `opensearch`, `pgvector`
- 默认值: `chroma`
- 描述: 指定要使用的向量数据库系统。此设置决定使用哪个向量存储系统来管理嵌入。

#### `RAG_EMBEDDING_ENGINE`

- 类型: `str` (枚举: `ollama`, `openai`)
- 选项:
  - 留空表示 `Default (SentenceTransformers)` - 使用 SentenceTransformers 进行嵌入。
  - `ollama` - 使用 Ollama API 进行嵌入。
  - `openai` - 使用 OpenAI API 进行嵌入。
- 描述: 选择用于 RAG 的嵌入引擎。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `RAG_EMBEDDING_MODEL`

- 类型: `str`
- 默认值: `sentence-transformers/all-MiniLM-L6-v2`
- 描述: 设置嵌入的模型。本地使用 Sentence-Transformer 模型。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `ENABLE_RAG_HYBRID_SEARCH`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用使用 `BM25` + `ChromaDB` 的集成搜索，并使用 `sentence_transformers` 模型进行重新排序。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `CONTENT_EXTRACTION_ENGINE`

- 类型: `str` (`tika`)
- 选项:
  - 留空以使用默认值
  - `tika` - 使用本地 Apache Tika 服务器
- 描述: 设置用于文档摄取的内容提取引擎。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `RAG_TOP_K`

- 类型: `int`
- 默认值: `3`
- 描述: 设置使用 RAG 时要考虑的默认结果数量。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `RAG_RELEVANCE_THRESHOLD`

- 类型: `float`
- 默认值: `0.0`
- 描述: 设置重新排序时考虑的文档相关性阈值。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `RAG_TEMPLATE`

- 类型: `str`
- 默认值: `DEFAULT_RAG_TEMPLATE` 环境变量的值。

模板:

```
### Task:
Respond to the user query using the provided context, incorporating inline citations in the format [source_id] **only when the <source_id> tag is explicitly provided** in the context.

### Guidelines:
- If you don't know the answer, clearly state that.
- If uncertain, ask the user for clarification.
- Respond in the same language as the user's query.
- If the context is unreadable or of poor quality, inform the user and provide the best possible answer.
- If the answer isn't present in the context but you possess the knowledge, explain this to the user and provide the answer using your own understanding.
- **Only include inline citations using [source_id] when a <source_id> tag is explicitly provided in the context.**  
- Do not cite if the <source_id> tag is not provided in the context.  
- Do not use XML tags in your response.
- Ensure citations are concise and directly related to the information provided.

### Example of Citation:
If the user asks about a specific topic and the information is found in "whitepaper.pdf" with a provided <source_id>, the response should include the citation like so:  
* "According to the study, the proposed method increases efficiency by 20% [whitepaper.pdf]."
If no <source_id> is present, the response should omit the citation.

### Output:
Provide a clear and direct response to the user's query, including inline citations in the format [source_id] only when the <source_id> tag is present in the context.

<context>
{{CONTEXT}}
</context>

<user_query>
{{QUERY}}
</user_query>
```

- 描述: 将 RAG 文档注入聊天完成时使用的模板
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `RAG_TEXT_SPLITTER`

- 类型: `str`
- 选项: `character`, `token`
- 默认值: `character`
- 描述: 设置 RAG 模型的文本分割器。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `TIKTOKEN_CACHE_DIR`

- 类型: `str`
- 默认值: `{CACHE_DIR}/tiktoken`
- 描述: 设置 TikiToken 缓存的目录。

#### `TIKTOKEN_ENCODING_NAME`

- 类型: `str`
- 默认值: `cl100k_base`
- 描述: 设置 TikiToken 的编码名称。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `CHUNK_SIZE`

- 类型: `int`
- 默认值: `1000`
- 描述: 设置嵌入文档的块大小。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `CHUNK_OVERLAP`

- 类型: `int`
- 默认值: `100`
- 描述: 指定块之间的重叠量。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `PDF_EXTRACT_IMAGES`

- 类型: `bool`
- 默认值: `False`
- 描述: 使用 OCR 从 PDF 中提取图像时加载文档。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `RAG_FILE_MAX_SIZE`

- 类型: `int`
- 描述: 设置可以上传以进行文档摄取的文件的最大大小。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `RAG_FILE_MAX_COUNT`

- 类型: `int`
- 描述: 设置可以一次上传以进行文档摄取的文件的最大数量。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

:::info

配置 `RAG_FILE_MAX_SIZE` 和 `RAG_FILE_MAX_COUNT` 时，请确保值合理，以防止过多的文件上传和潜在的性能问题。

:::

#### `RAG_RERANKING_MODEL`

- 类型: `str`
- 描述: 设置重新排序结果的模型。本地使用 Sentence-Transformer 模型。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `RAG_OPENAI_API_BASE_URL`

- 类型: `str`
- 默认值: `${OPENAI_API_BASE_URL}`
- 描述: 设置用于 RAG 嵌入的 OpenAI 基础 API URL。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `RAG_OPENAI_API_KEY`

- 类型: `str`
- 默认值: `${OPENAI_API_KEY}`
- 描述: 设置用于 RAG 嵌入的 OpenAI API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `RAG_EMBEDDING_OPENAI_BATCH_SIZE`

- 类型: `int`
- 默认值: `1`
- 描述: 设置 OpenAI 嵌入的批次大小。

#### `RAG_EMBEDDING_BATCH_SIZE`

- 类型: `int`
- 默认值: `1`
- 描述: 设置 RAG（检索增强生成器）模型中嵌入的批次大小。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `RAG_OLLAMA_API_KEY`

- 类型: `str`
- 描述: 设置用于 RAG 模型的 Ollama API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `RAG_OLLAMA_BASE_URL`

- 类型: `str`
- 描述: 设置用于 RAG 模型的 Ollama API 基础 URL。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `ENABLE_RETRIEVAL_QUERY_GENERATION`

- 类型: `bool`
- 默认值: `True`
- 描述: 启用或禁用检索查询生成。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `QUERY_GENERATION_PROMPT_TEMPLATE`

- 类型: `str`
- 默认值: `DEFAULT_QUERY_GENERATION_PROMPT_TEMPLATE` 环境变量的值。

模板:

```
### Task:
Analyze the chat history to determine the necessity of generating search queries, in the given language. By default, **prioritize generating 1-3 broad and relevant search queries** unless it is absolutely certain that no additional information is required. The aim is to retrieve comprehensive, updated, and valuable information even with minimal uncertainty. If no search is unequivocally needed, return an empty list.

### Guidelines:
- Respond **EXCLUSIVELY** with a JSON object. Any form of extra commentary, explanation, or additional text is strictly prohibited.
- When generating search queries, respond in the format: { "queries": ["query1", "query2"] }, ensuring each query is distinct, concise, and relevant to the topic.
- If and only if it is entirely certain that no useful results can be retrieved by a search, return: { "queries": [] }.
- Err on the side of suggesting search queries if there is **any chance** they might provide useful or updated information.
- Be concise and focused on composing high-quality search queries, avoiding unnecessary elaboration, commentary, or assumptions.
- Today's date is: {{CURRENT_DATE}}.
- Always prioritize providing actionable and broad queries that maximize informational coverage.

### Output:
Strictly return in JSON format: 
{
  "queries": ["query1", "query2"]
}

### Chat History:
<chat_history>
{{MESSAGES:END:6}}
</chat_history>
```

- 描述: 设置查询生成的提示模板。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

### Apache Tika

#### `TIKA_SERVER_URL`

- 类型: `str`
- 默认值: `http://localhost:9998`
- 描述: 设置 Apache Tika 服务器的 URL。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

### ChromaDB

#### `CHROMA_TENANT`

- 类型: `str`
- 默认值: `chromadb.DEFAULT_TENANT` 的值（`chromadb` 模块中的常量）
- 描述: 为 ChromaDB 设置租户以用于 RAG 嵌入。

#### `CHROMA_DATABASE`

- 类型: `str`
- 默认值: `chromadb.DEFAULT_DATABASE` 的值（`chromadb` 模块中的常量）
- 描述: 设置 ChromaDB 租户中用于 RAG 嵌入的数据库。

#### `CHROMA_HTTP_HOST`

- 类型: `str`
- 描述: 指定远程 ChromaDB 服务器的主机名。如果未设置，则使用本地 ChromaDB 实例。

#### `CHROMA_HTTP_PORT`

- 类型: `int`
- 默认值: `8000`
- 描述: 指定远程 ChromaDB 服务器的端口。

#### `CHROMA_HTTP_HEADERS`

- 类型: `str`
- 描述: 包含在每个 ChromaDB 请求中的 HTTP 标头的逗号分隔列表。
- 示例: `Authorization=Bearer heuhagfuahefj,User-Agent=OpenWebUI`。

#### `CHROMA_HTTP_SSL`

- 类型: `bool`
- 默认值: `False`
- 描述: 控制是否为 ChromaDB 服务器连接使用 SSL。

#### `CHROMA_CLIENT_AUTH_PROVIDER`

- 类型: `str`
- 描述: 指定远程 ChromaDB 服务器的身份验证提供者。
- 示例: `chromadb.auth.basic_authn.BasicAuthClientProvider`

#### `CHROMA_CLIENT_AUTH_CREDENTIALS`

- 类型: `str`
- 描述: 指定远程 ChromaDB 服务器的身份验证凭证。
- 示例: `username:password`

### Google Drive

#### `ENABLE_GOOGLE_DRIVE_INTEGRATION`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用或禁用 Google Drive 集成。如果设置为 true，并且 `GOOGLE_DRIVE_CLIENT_ID` 和 `GOOGLE_DRIVE_API_KEY` 都已配置，Google Drive 将作为聊天 UI 中的上传选项出现。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

:::info

启用 `GOOGLE_DRIVE_INTEGRATION` 时，请确保已正确配置 `GOOGLE_DRIVE_CLIENT_ID` 和 `GOOGLE_DRIVE_API_KEY`，并审查 Google 的服务条款和使用指南。

:::

#### `GOOGLE_DRIVE_CLIENT_ID`

- 类型: `str`
- 描述: 设置 Google Drive 的客户端 ID（客户端必须配置了已启用的 Drive API 和 Picker API）。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `GOOGLE_DRIVE_API_KEY`

- 类型: `str`
- 描述: 设置 Google Drive 集成的 API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

### Milvus

#### `MILVUS_URI`

- 类型: `str`
- 默认值: `${DATA_DIR}/vector_db/milvus.db`
- 描述: 指定用于连接到 Milvus 向量数据库的 URI。根据部署配置，这可以指向本地或远程 Milvus 服务器。

### OpenSearch

#### `OPENSEARCH_CERT_VERIFY`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用或禁用 OpenSearch 证书验证。

#### `OPENSEARCH_PASSWORD`

- 类型: `str`
- 描述: 设置 OpenSearch 的密码。

#### `OPENSEARCH_SSL`

- 类型: `bool`
- 默认值: `True`
- 描述: 启用或禁用 OpenSearch 的 SSL。

#### `OPENSEARCH_URI`

- 类型: `str`
- 默认值: `https://localhost:9200`
- 描述: 设置 OpenSearch 的 URI。

#### `OPENSEARCH_USERNAME`

- 类型: `str`
- 描述: 设置 OpenSearch 的用户名。

### PGVector

#### `PGVECTOR_DB_URL`

- 类型: `str`
- 默认值: `DATABASE_URL` 环境变量的值
- 描述: 设置模型存储的数据库 URL。

### Qdrant

#### `QDRANT_API_KEY`

- 类型: `str`
- 描述: 设置 Qdrant 的 API 密钥。

#### `QDRANT_URI`

- 类型: `str`
- 描述: 设置 Qdrant 的 URI。

## 网络搜索

#### `ENABLE_RAG_WEB_SEARCH`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用网络搜索切换
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `ENABLE_SEARCH_QUERY_GENERATION`

- 类型: `bool`
- 默认值: `True`
- 描述: 启用或禁用搜索查询生成。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `RAG_WEB_SEARCH_RESULT_COUNT`

- 类型: `int`
- 默认值: `3`
- 描述: 要抓取的最大搜索结果数。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `RAG_WEB_SEARCH_CONCURRENT_REQUESTS`

- 类型: `int`
- 默认值: `10`
- 描述: 抓取搜索结果返回的网页的并发请求数。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `RAG_WEB_SEARCH_ENGINE`

- 类型: `str` (枚举: `searxng`, `google_pse`, `brave`, `kagi`, `mojeek`, `serpstack`, `serper`, `serply`, `searchapi`, `duckduckgo`, `tavily`, `jina`, `bing`)
- 选项:
  - `searxng` - 使用 [SearXNG](https://github.com/searxng/searxng) 搜索引擎。
  - `google_pse` - 使用 [Google 可编程搜索引擎](https://programmablesearchengine.google.com/about/)。
  - `brave` - 使用 [Brave 搜索引擎](https://brave.com/search/api/)。
  - `kagi` - 使用 [Kagi](https://www.kagi.com/) 搜索引擎。
  - `mojeek` - 使用 [Mojeek](https://www.mojeek.com/) 搜索引擎。
  - `serpstack` - 使用 [Serpstack](https://serpstack.com/) 搜索引擎。
  - `serper` - 使用 [Serper](https://serper.dev/) 搜索引擎。
  - `serply` - 使用 [Serply](https://serply.io/) 搜索引擎。
  - `searchapi` - 使用 [SearchAPI](https://www.searchapi.io/) 搜索引擎。
  - `duckduckgo` - 使用 [DuckDuckGo](https://duckduckgo.com/) 搜索引擎。
  - `tavily` - 使用 [Tavily](https://tavily.com/) 搜索引擎。
  - `jina` - 使用 [Jina](https://jina.ai/) 搜索引擎。
  - `bing` - 使用 [Bing](https://www.bing.com/) 搜索引擎。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `SEARXNG_QUERY_URL`

- 类型: `str`
- 描述: 支持 JSON 输出的 [SearXNG 搜索 API](https://docs.searxng.org/dev/search_api.html) URL。`` 被替换为
搜索查询。例如: `http://searxng.local/search?q=`
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `GOOGLE_PSE_API_KEY`

- 类型: `str`
- 描述: 设置 Google 可编程搜索引擎（PSE）服务的 API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `GOOGLE_PSE_ENGINE_ID`

- 类型: `str`
- 描述: Google 可编程搜索引擎（PSE）服务的引擎 ID。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `BRAVE_SEARCH_API_KEY`

- 类型: `str`
- 描述: 设置 Brave 搜索 API 的 API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `KAGI_SEARCH_API_KEY`

- 类型: `str`
- 描述: 设置 Kagi 搜索 API 的 API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `MOJEEK_SEARCH_API_KEY`

- 类型: `str`
- 描述: 设置 Mojeek 搜索 API 的 API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `SERPSTACK_API_KEY`

- 类型: `str`
- 描述: 设置 Serpstack 搜索 API 的 API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `SERPSTACK_HTTPS`

- 类型: `bool`
- 默认值: `True`
- 描述: 配置 Serpstack 请求的 HTTPS 使用。免费层请求仅限于 HTTP。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `SERPER_API_KEY`

- 类型: `str`
- 描述: 设置 Serper 搜索 API 的 API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `SERPLY_API_KEY`

- 类型: `str`
- 描述: 设置 Serply 搜索 API 的 API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `SEARCHAPI_API_KEY`

- 类型: `str`
- 描述: 设置 SearchAPI 的 API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `SEARCHAPI_ENGINE`

- 类型: `str`
- 描述: 设置 SearchAPI 引擎。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `TAVILY_API_KEY`

- 类型: `str`
- 描述: 设置 Tavily 搜索 API 的 API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `JINA_API_KEY`

- 类型: `str`
- 描述: 设置 Jina 的 API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `BING_SEARCH_V7_ENDPOINT`

- 类型: `str`
- 描述: 设置 Bing 搜索 API 的端点。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `BING_SEARCH_V7_SUBSCRIPTION_KEY`

- 类型: `str`
- 默认值: `https://api.bing.microsoft.com/v7.0/search`
- 描述: 设置 Bing 搜索 API 的订阅密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

### YouTube 加载器

#### `YOUTUBE_LOADER_PROXY_URL`

- 类型: `str`
- 描述: 设置 YouTube 加载器的代理 URL。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `YOUTUBE_LOADER_LANGUAGE`

- 类型: `str`
- 默认值: `en`
- 描述: 设置 YouTube 视频加载的语言。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

## 音频

### Whisper 语音转文本（本地）

#### `WHISPER_MODEL`

- 类型: `str`
- 默认值: `base`
- 描述: 设置用于语音转文本的 Whisper 模型。使用的是 faster_whisper 后端，量化为 `int8`。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `WHISPER_MODEL_DIR`

- 类型: `str`
- 默认值: `${DATA_DIR}/cache/whisper/models`
- 描述: 指定存储 Whisper 模型文件的目录。

### 语音转文本（OpenAI）

#### `AUDIO_STT_ENGINE`

- 类型: `str` (枚举: `openai`)
- 选项:
  - 留空使用本地 Whisper 引擎进行语音转文本。
  - `openai` - 使用 OpenAI 引擎进行语音转文本。
- 描述: 指定要使用的语音转文本引擎。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `AUDIO_STT_MODEL`

- 类型: `str`
- 默认值: `whisper-1`
- 描述: 指定用于 OpenAI 兼容端点的语音转文本模型。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `AUDIO_STT_OPENAI_API_BASE_URL`

- 类型: `str`
- 默认值: `${OPENAI_API_BASE_URL}`
- 描述: 设置用于语音转文本的 OpenAI 兼容基础 URL。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `AUDIO_STT_OPENAI_API_KEY`

- 类型: `str`
- 默认值: `${OPENAI_API_KEY}`
- 描述: 设置用于语音转文本的 OpenAI API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

### 文本转语音

#### `AUDIO_TTS_API_KEY`

- 类型: `str`
- 描述: 设置文本转语音的 API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `AUDIO_TTS_ENGINE`

- 类型: `str` (枚举: `azure`, `elevenlabs`, `openai`, `transformers`)
- 选项:
  - 留空使用内置 WebAPI 引擎进行文本转语音。
  - `azure` - 使用 Azure 引擎进行文本转语音。
  - `elevenlabs` - 使用 ElevenLabs 引擎进行文本转语音。
  - `openai` - 使用 OpenAI 引擎进行文本转语音。
  - `transformers` - 使用 SentenceTransformers 进行文本转语音。
- 描述: 指定要使用的文本转语音引擎。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `AUDIO_TTS_MODEL`

- 类型: `str`
- 默认值: `tts-1`
- 描述: 指定要使用的 OpenAI 文本转语音模型。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

### Azure 文本转语音

#### `AUDIO_TTS_AZURE_SPEECH_OUTPUT_FORMAT`

- 类型: `str`
- 描述: 设置 Azure 文本转语音的输出格式。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `AUDIO_TTS_AZURE_SPEECH_REGION`

- 类型: `str`
- 描述: 设置 Azure 文本转语音的区域。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

### OpenAI 文本转语音

#### `AUDIO_TTS_OPENAI_API_BASE_URL`

- 类型: `str`
- 默认值: `${OPENAI_API_BASE_URL}`
- 描述: 设置用于文本转语音的 OpenAI 兼容基础 URL。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `AUDIO_TTS_OPENAI_API_KEY`

- 类型: `str`
- 默认值: `${OPENAI_API_KEY}`
- 描述: 设置用于文本转语音的 API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `AUDIO_TTS_SPLIT_ON`

- 类型: `str`
- 默认值: `punctuation`
- 描述: 设置 OpenAI 文本转语音的分隔方式。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `AUDIO_TTS_VOICE`

- 类型: `str`
- 默认值: `alloy`
- 描述: 设置 OpenAI 文本转语音的声音。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

## 图片生成

#### `ENABLE_IMAGE_GENERATION`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用或禁用图像生成功能。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `IMAGE_GENERATION_ENGINE`

- 类型: `str` (枚举: `openai`, `comfyui`, `automatic1111`)
- 选项:
  - `openai` - 使用 OpenAI DALL-E 进行图像生成。
  - `comfyui` - 使用 ComfyUI 引擎进行图像生成。
  - `automatic1111` - 使用 Automatic1111 引擎进行图像生成（默认）。
- 默认值: `openai`
- 描述: 指定用于图像生成的引擎。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `IMAGE_GENERATION_MODEL`

- 类型: `str`
- 描述: 默认用于图像生成的模型
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `IMAGE_SIZE`

- 类型: `str`
- 默认值: `512x512`
- 描述: 设置要生成的默认图像大小。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `IMAGE_STEPS`

- 类型: `int`
- 默认值: `50`
- 描述: 设置图像生成的默认迭代步骤。用于 ComfyUI 和 AUTOMATIC1111。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

### AUTOMATIC1111

#### `AUTOMATIC1111_API_AUTH`

- 类型: `str`
- 描述: 设置 Automatic1111 API 认证。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `AUTOMATIC1111_BASE_URL`

- 类型: `str`
- 描述: 指定 Automatic1111 的稳定扩散 API 的 URL。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `AUTOMATIC1111_CFG_SCALE`

- 类型: `float`
- 描述: 设置 Automatic1111 推理的比例。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `AUTOMATIC1111_SAMPLER`

- 类型: `str`
- 描述: 设置 Automatic1111 推理的采样器。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `AUTOMATIC1111_SCHEDULER`

- 类型: `str`
- 描述: 设置 Automatic1111 推理的调度器。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

### ComfyUI

#### `COMFYUI_BASE_URL`

- 类型: `str`
- 描述: 指定 ComfyUI 图像生成 API 的 URL。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `COMFYUI_API_KEY`

- 类型: `str`
- 描述: 设置 ComfyUI 的 API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `COMFYUI_WORKFLOW`

- 类型: `str`
- 默认值:

```
{
  "3": {
    "inputs": {
      "seed": 0,
      "steps": 20,
      "cfg": 8,
      "sampler_name": "euler",
      "scheduler": "normal",
      "denoise": 1,
      "model": [
        "4",
        0
      ],
      "positive": [
        "6",
        0
      ],
      "negative": [
        "7",
        0
      ],
      "latent_image": [
        "5",
        0
      ]
    },
    "class_type": "KSampler",
    "_meta": {
      "title": "KSampler"
    }
  },
  "4": {
    "inputs": {
      "ckpt_name": "model.safetensors"
    },
    "class_type": "CheckpointLoaderSimple",
    "_meta": {
      "title": "Load Checkpoint"
    }
  },
  "5": {
    "inputs": {
      "width": 512,
      "height": 512,
      "batch_size": 1
    },
    "class_type": "EmptyLatentImage",
    "_meta": {
      "title": "Empty Latent Image"
    }
  },
  "6": {
    "inputs": {
      "text": "Prompt",
      "clip": [
        "4",
        1
      ]
    },
    "class_type": "CLIPTextEncode",
    "_meta": {
      "title": "CLIP Text Encode (Prompt)"
    }
  },
  "7": {
    "inputs": {
      "text": "",
      "clip": [
        "4",
        1
      ]
    },
    "class_type": "CLIPTextEncode",
    "_meta": {
      "title": "CLIP Text Encode (Prompt)"
    }
  },
  "8": {
    "inputs": {
      "samples": [
        "3",
        0
      ],
      "vae": [
        "4",
        2
      ]
    },
    "class_type": "VAEDecode",
    "_meta": {
      "title": "VAE Decode"
    }
  },
  "9": {
    "inputs": {
      "filename_prefix": "ComfyUI",
      "images": [
        "8",
        0
      ]
    },
    "class_type": "SaveImage",
    "_meta": {
      "title": "Save Image"
    }
  }
}
```

- 描述: 设置 ComfyUI 工作流。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

### OpenAI DALL-E

#### `IMAGES_OPENAI_API_BASE_URL`

- 类型: `str`
- 默认值: `${OPENAI_API_BASE_URL}`
- 描述: 设置用于 DALL-E 图像生成的 OpenAI 兼容基础 URL。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `IMAGES_OPENAI_API_KEY`

- 类型: `str`
- 默认值: `${OPENAI_API_KEY}`
- 描述: 设置用于 DALL-E 图像生成的 API 密钥。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

## OAuth

#### `ENABLE_OAUTH_SIGNUP`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用通过 OAuth 注册时的账户创建。与 `ENABLE_SIGNUP` 不同。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

:::danger

`ENABLE_OAUTH_SIGNUP` 设置为 `True` 时，必须将 `ENABLE_LOGIN_FORM` 设置为 `False`。否则将无法登录。

:::

#### `ENABLE_API_KEY`

- 类型: `bool`
- 默认值: `True`
- 描述: 启用 API 密钥身份验证。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `ENABLE_OAUTH_ROLE_MANAGEMENT`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用角色管理以进行 oauth 授权。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `ENABLE_OAUTH_GROUP_MANAGEMENT`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用或禁用 OAUTH 组管理。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OAUTH_MERGE_ACCOUNTS_BY_EMAIL`

- 类型: `bool`
- 默认值: `False`
- 描述: 如果启用，通过电子邮件地址将 OAuth 账户与现有账户合并。这被视为不安全，因为不是所有 OAuth 提供商都会验证电子邮件地址，并可能导致
潜在的账户接管。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OAUTH_USERNAME_CLAIM`

- 类型: `str`
- 默认值: `name`
- 描述: 设置 OpenID 的用户名声明。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OAUTH_EMAIL_CLAIM`

- 类型: `str`
- 默认值: `email`
- 描述: 设置 OpenID 的电子邮件声明。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OAUTH_PICTURE_CLAIM`

- 类型: `str`
- 默认值: `picture`
- 描述: 设置 OpenID 的图片（头像）声明。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OAUTH_GROUP_CLAIM`

- 类型: `str`
- 默认值: `groups`
- 描述: 指定 OAUTH 认证的组声明。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OAUTH_ROLES_CLAIM`

- 类型: `str`
- 默认值: `roles`
- 描述: 设置在 OIDC 令牌中查找的角色声明。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OAUTH_SCOPES`

- 类型: `str`
- 默认值: `openid email profile`
- 描述: 设置 OIDC 认证的范围。`openid` 和 `email` 是必需的。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OAUTH_ALLOWED_DOMAINS`

- 类型: `str`
- 默认值: `*`
- 描述: 指定 OAUTH 认证的允许域。（例如 "example1.com,example2.com"）。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OAUTH_ALLOWED_ROLES`

- 类型: `str`
- 默认值: `user,admin`
- 描述: 设置允许访问平台的角色。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OAUTH_ADMIN_ROLES`

- 类型: `str`
- 默认值: `admin`
- 描述: 设置被视为管理员的角色。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `WEBUI_AUTH_TRUSTED_EMAIL_HEADER`

- 类型: `str`
- 描述: 定义用于身份验证的受信请求标头。请参阅 [SSO 文档](/features/sso)。

#### `WEBUI_AUTH_TRUSTED_NAME_HEADER`

- 类型: `str`
- 描述: 定义用于注册时使用 `WEBUI_AUTH_TRUSTED_EMAIL_HEADER` 标头的用户名的受信请求标头。请参阅 [SSO 文档](/features/sso)。

### Google

请参阅 https://support.google.com/cloud/answer/6158849?hl=zh-cn

#### `GOOGLE_CLIENT_ID`

- 类型: `str`
- 描述: 设置 Google OAuth 的客户端 ID
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `GOOGLE_CLIENT_SECRET`

- 类型: `str`
- 描述: 设置 Google OAuth 的客户端秘密
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `GOOGLE_OAUTH_SCOPE`

- 类型: `str`
- 默认值: `openid email profile`
- 描述: 设置 Google OAuth 认证的范围。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `GOOGLE_REDIRECT_URI`

- 类型: `str`
- 默认值: `/oauth/google/callback`
- 描述: 设置 Google OAuth 的重定向 URI
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

### Microsoft

请参阅 https://learn.microsoft.com/en-us/entra/identity-platform/quickstart-register-app

#### `MICROSOFT_CLIENT_ID`

- 类型: `str`
- 描述: 设置 Microsoft OAuth 的客户端 ID
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `MICROSOFT_CLIENT_SECRET`

- 类型: `str`
- 描述: 设置 Microsoft OAuth 的客户端秘密
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `MICROSOFT_CLIENT_TENANT_ID`

- 类型: `str`
- 描述: 设置 Microsoft OAuth 的租户 ID
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `MICROSOFT_OAUTH_SCOPE`

- 类型: `str`
- 默认值: `openid email profile`
- 描述: 设置 Microsoft OAuth 认证的范围。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `MICROSOFT_REDIRECT_URI`

- 类型: `str`
- 默认值: `/oauth/microsoft/callback`
- 描述: 设置 Microsoft OAuth 的重定向 URI
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

### OpenID (OIDC)

#### `OAUTH_CLIENT_ID`

- 类型: `str`
- 描述: 设置 OIDC 的客户端 ID
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OAUTH_CLIENT_SECRET`

- 类型: `str`
- 描述: 设置 OIDC 的客户端秘密
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OPENID_PROVIDER_URL`

- 类型: `str`
- 描述: `.well-known/openid-configuration` 端点的路径
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OAUTH_PROVIDER_NAME`

- 类型: `str`
- 默认值: `SSO`
- 描述: 设置 OIDC 提供者的名称。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `OPENID_REDIRECT_URI`

- 类型: `str`
- 默认值: `/oauth/oidc/callback`
- 描述: 设置 OIDC 的重定向 URI
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

## LDAP

#### `ENABLE_LDAP

- 类型: `bool`
- 默认值: `False`
- 描述: 启用或禁用 LDAP 身份验证。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `LDAP_APP_DN`

- 类型: `str`
- 描述: 设置 LDAP 应用程序的专有名称。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `LDAP_APP_PASSWORD`

- 类型: `str`
- 描述: 设置 LDAP 应用程序的密码。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `LDAP_ATTRIBUTE_FOR_USERNAME`

- 类型: `str`
- 描述: 设置用于 LDAP 身份验证的用户名属性。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `LDAP_CA_CERT_FILE`

- 类型: `str`
- 描述: 设置 LDAP CA 证书文件的路径。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `LDAP_CIPHERS`

- 类型: `str`
- 默认值: `ALL`
- 描述: 设置用于 LDAP 连接的加密套件。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `LDAP_SEARCH_BASE`

- 类型: `str`
- 描述: 设置用于 LDAP 身份验证的搜索基础。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `LDAP_SEARCH_FILTERS`

- 类型: `str`
- 描述: 设置用于 LDAP 搜索的过滤器。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `LDAP_SERVER_HOST`

- 类型: `str`
- 默认值: `localhost`
- 描述: 设置 LDAP 服务器的主机名。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `LDAP_SERVER_LABEL`

- 类型: `str`
- 描述: 设置 LDAP 服务器的标签。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `LDAP_SERVER_PORT`

- 类型: `int`
- 默认值: `389`
- 描述: 设置 LDAP 服务器的端口号。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `LDAP_USE_TLS`

- 类型: `bool`
- 默认值: `True`
- 描述: 启用或禁用 LDAP 连接的 TLS。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

## 工作区权限

#### `USER_PERMISSIONS_WORKSPACE_MODELS_ACCESS`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用或禁用用户访问工作区模型的权限。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `USER_PERMISSIONS_WORKSPACE_KNOWLEDGE_ACCESS`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用或禁用用户访问工作区知识的权限。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `USER_PERMISSIONS_WORKSPACE_PROMPTS_ACCESS`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用或禁用用户访问工作区提示的权限。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `USER_PERMISSIONS_WORKSPACE_TOOLS_ACCESS`

- 类型: `bool`
- 默认值: `False`
- 描述: 启用或禁用用户访问工作区工具的权限。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

## 聊天权限

#### `USER_PERMISSIONS_CHAT_FILE_UPLOAD`

- 类型: `bool`
- 默认值: `True`
- 描述: 启用或禁用用户上传文件到聊天的权限。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `USER_PERMISSIONS_CHAT_DELETE`

- 类型: `bool`
- 默认值: `True`
- 描述: 启用或禁用用户删除聊天的权限。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `USER_PERMISSIONS_CHAT_EDIT`

- 类型: `bool`
- 默认值: `True`
- 描述: 启用或禁用用户编辑聊天的权限。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

#### `USER_PERMISSIONS_CHAT_TEMPORARY`

- 类型: `bool`
- 默认值: `True`
- 描述: 启用或禁用用户创建临时聊天的权限。
- 持久性: 此环境变量是一个 `PersistentConfig` 变量。

## 杂项环境变量

这些变量并不限于 Open WebUI，但在某些情况下仍然有价值。

### Amazon S3 存储

#### `STORAGE_PROVIDER`

- 类型: `str`
- 默认值: 空字符串 (' ')，默认为 `local`
- 描述: 设置存储提供商。

#### `S3_ACCESS_KEY_ID`

- 类型: `str`
- 描述: 设置 S3 存储的访问密钥 ID。

#### `S3_BUCKET_NAME`

- 类型: `str`
- 描述: 设置 S3 存储的桶名称。

#### `S3_ENDPOINT_URL`

- 类型: `str`
- 描述: 设置 S3 存储的端点 URL。

#### `S3_REGION_NAME`

- 类型: `str`
- 描述: 设置 S3 存储的区域名称。

#### `S3_SECRET_ACCESS_KEY`

- 类型: `str`
- 描述: 设置 S3 存储的秘密访问密钥。

### 数据库池

#### `DATABASE_URL`

- 类型: `str`
- 默认值: `sqlite:///${DATA_DIR}/webui.db`
- 描述: 指定要连接到的数据库 URL。

:::info

支持 SQLite 和 Postgres。更改 URL 不会在数据库之间迁移数据。
有关 URL 方案的文档可在[此处](https://docs.sqlalchemy.org/en/20/core/engines.html#database-urls)找到。

:::

#### `DATABASE_POOL_SIZE`

- 类型: `int`
- 默认值: `0`
- 描述: 指定数据库池的大小。值为 `0` 表示禁用池。

#### `DATABASE_POOL_MAX_OVERFLOW`

- 类型: `int`
- 默认值: `0`
- 描述: 指定数据库池的最大溢出。

:::info

有关此设置的更多信息，请参阅[此处](https://docs.sqlalchemy.org/en/20/core/pooling.html#sqlalchemy.pool.QueuePool.params.max_overflow)。

:::

#### `DATABASE_POOL_TIMEOUT`

- 类型: `int`
- 默认值: `30`
- 描述: 指定获取连接的数据库池超时时间（以秒为单位）。

:::info

有关此设置的更多信息，请参阅[此处](https://docs.sqlalchemy.org/en/20/core/pooling.html#sqlalchemy.pool.QueuePool.params.timeout)。

:::

#### `DATABASE_POOL_RECYCLE`

- 类型: `int`
- 默认值: `3600`
- 描述: 指定数据库池的回收时间（以秒为单位）。

:::info

有关此设置的更多信息，请参阅[此处](https://docs.sqlalchemy.org/en/20/core/pooling.html#setting-pool-recycle)。

:::

### Redis

#### `ENABLE_WEBSOCKET_SUPPORT`

- 类型: `bool`
- 默认值: `False`
- 描述: 在 Open WebUI 中启用 websocket 支持（与 Redis 一起使用）。

#### `WEBSOCKET_MANAGER`

- 类型: `str`
- 默认值: `redis`
- 描述: 指定要使用的 websocket 管理器（在此情况下为 Redis）。

#### `WEBSOCKET_REDIS_URL` (`REDIS_URL` 存在于潜在未来用例中。在实践中，建议同时设置。)

- 类型: `str`
- 默认值: `redis://localhost:6379/0`
- 描述: 指定用于 websocket 通信的 Redis 实例的 URL。

### 代理设置

Open WebUI 支持使用代理进行 HTTP 和 HTTPS 检索。要指定代理设置，
Open WebUI 使用以下环境变量：

#### `http_proxy`

- 类型: `str`
- 描述: 设置 HTTP 代理的 URL。

#### `https_proxy`

- 类型: `str`
- 描述: 设置 HTTPS 代理的 URL。

#### `no_proxy`

- 类型: `str`
- 描述: 列出不应使用代理的域扩展名（或 IP 地址），以逗号分隔。例如，将 no_proxy 设置为 '.mit.edu' 确保在访问来自 MIT 的文档时绕过代理。
