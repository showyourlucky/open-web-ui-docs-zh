---
sidebar_position: 400
title: "🔗 API 端点"
---

本指南提供了如何有效使用我们的模型进行无缝集成和自动化的 API 端点相关信息。请注意，这是一个实验性设置，未来可能会有更新以提升功能。

## 身份验证

为了确保安全访问 API，需要进行身份验证 🛡️。您可以使用 Bearer Token 机制对 API 请求进行身份验证。您可以在 Open WebUI 的 **设置 > 帐户** 中获取您的 API 密钥，或者使用 JWT（JSON Web Token）进行身份验证。

## 主要 API 端点

### 📜 获取所有模型

- **端点**: `GET /api/models`
- **描述**: 获取通过 Open WebUI 创建或添加的所有模型。
- **示例**:

  ```bash
  curl -H "Authorization: Bearer YOUR_API_KEY" http://localhost:3000/api/models
  ```

### 💬 聊天补全

- **端点**: `POST /api/chat/completions`
- **描述**: 这是一个与 OpenAI API 兼容的聊天补全端点，适用于 Open WebUI 上的模型，包括 Ollama 模型、OpenAI 模型和 Open WebUI 函数模型。
- **示例**:

  ```bash
  curl -X POST http://localhost:3000/api/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "model": "llama3.1",
        "messages": [
          {
            "role": "user",
            "content": "为什么天空是蓝色的？"
          }
        ]
      }'
  ```

### 🧩 检索增强生成 (RAG)

检索增强生成 (RAG) 功能允许您通过结合外部数据来增强响应。以下内容介绍了如何通过 API 管理文件和知识集合，并在聊天补全中有效地使用它们。

#### 上传文件

要在 RAG 响应中使用外部数据，首先需要上传文件。上传文件的内容将自动提取并存储在向量数据库中。

- **端点**: `POST /api/v1/files/`
- **Curl 示例**:

  ```bash
  curl -X POST -H "Authorization: Bearer YOUR_API_KEY" -H "Accept: application/json" \
  -F "file=@/path/to/your/file" http://localhost:3000/api/v1/files/
  ```

- **Python 示例**:

  ```python
  import requests

  def upload_file(token, file_path):
      url = 'http://localhost:3000/api/v1/files/'
      headers = {
          'Authorization': f'Bearer {token}',
          'Accept': 'application/json'
      }
      files = {'file': open(file_path, 'rb')}
      response = requests.post(url, headers=headers, files=files)
      return response.json()
  ```

#### 将文件添加到知识集合

上传后，您可以将文件分组为知识集合，或者在聊天中单独引用它们。

- **端点**: `POST /api/v1/knowledge/{id}/file/add`
- **Curl 示例**:

  ```bash
  curl -X POST http://localhost:3000/api/v1/knowledge/{knowledge_id}/file/add \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"file_id": "your-file-id-here"}'
  ```

- **Python 示例**:

  ```python
  import requests

  def add_file_to_knowledge(token, knowledge_id, file_id):
      url = f'http://localhost:3000/api/v1/knowledge/{knowledge_id}/file/add'
      headers = {
          'Authorization': f'Bearer {token}',
          'Content-Type': 'application/json'
      }
      data = {'file_id': file_id}
      response = requests.post(url, headers=headers, json=data)
      return response.json()
  ```

#### 在聊天补全中使用文件和集合

您可以在 RAG 查询中引用单个文件或整个集合，以获得更丰富的响应。

##### 使用单个文件进行聊天补全

当您希望让聊天模型的响应集中在特定文件的内容上时，这种方法非常有用。

- **端点**: `POST /api/chat/completions`
- **Curl 示例**:

  ```bash
  curl -X POST http://localhost:3000/api/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "model": "gpt-4-turbo",
        "messages": [
          {"role": "user", "content": "解释这份文档中的概念。"}
        ],
        "files": [
          {"type": "file", "id": "your-file-id-here"}
        ]
      }'
  ```

- **Python 示例**:

  ```python
  import requests

  def chat_with_file(token, model, query, file_id):
      url = 'http://localhost:3000/api/chat/completions'
      headers = {
          'Authorization': f'Bearer {token}',
          'Content-Type': 'application/json'
      }
      payload = {
          'model': model,
          'messages': [{'role': 'user', 'content': query}],
          'files': [{'type': 'file', 'id': file_id}]
      }
      response = requests.post(url, headers=headers, json=payload)
      return response.json()
  ```

##### 使用知识集合进行聊天补全

当查询可能从更广泛的上下文或多份文档中受益时，可以利用知识集合来增强响应。

- **端点**: `POST /api/chat/completions`
- **Curl 示例**:

  ```bash
  curl -X POST http://localhost:3000/api/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "model": "gpt-4-turbo",
        "messages": [
          {"role": "user", "content": "提供关于集合中涵盖的历史视角的见解。"}
        ],
        "files": [
          {"type": "collection", "id": "your-collection-id-here"}
        ]
      }'
  ```

- **Python 示例**:

  ```python
  import requests

  def chat_with_collection(token, model, query, collection_id):
      url = 'http://localhost:3000/api/chat/completions'
      headers = {
          'Authorization': f'Bearer {token}',
          'Content-Type': 'application/json'
      }
      payload = {
          'model': model,
          'messages': [{'role': 'user', 'content': query}],
          'files': [{'type': 'collection', 'id': collection_id}]
      }
      response = requests.post(url, headers=headers, json=payload)
      return response.json()
  ```

这些方法使您能够通过上传的文件和精心策划的知识集合有效地利用外部知识，从而增强基于 Open WebUI API 的聊天应用的功能。无论是单独使用文件还是在集合中使用，您都可以根据具体需求自定义集成。

## 使用 Open WebUI 作为统一 LLM 提供商的优势

Open WebUI 提供了诸多优势，使其成为开发者和企业的必备工具：

- **统一界面**: 通过单一的集成平台简化与不同 LLM 的交互。
- **易于实现**: 快速启动集成，提供全面的文档和社区支持。

## Swagger 文档链接

:::important
请确保将 `ENV` 环境变量设置为 `dev`，以便访问任何服务的 Swagger 文档。如果没有此配置，文档将不可用。
:::

访问 Open WebUI 提供的不同服务的详细 API 文档：

| 应用程序 | 文档路径      |
|-------------|-------------------------|
| 主应用       | `/docs`                 |


遵循这些指南，您可以快速集成并开始使用 Open WebUI API。如果您遇到任何问题或有任何疑问，请随时通过我们的 Discord 社区联系我们，或查阅常见问题解答。祝您编码愉快！ 🌟
