---
sidebar_position: 5
title: "🔗 URL 参数"
---

在 Open WebUI 中，您可以通过各种 URL 参数自定义聊天会话。这些参数允许您为每个聊天设置特定的配置、启用功能并定义模型设置。这种方法提供了直接从 URL 对单个聊天会话进行灵活控制的能力。

## URL 参数概览

下表列出了可用的 URL 参数、它们的功能及使用示例。

| **参数**      | **描述**                                                                  | **示例**                          |
|-----------------------|----------------------------------------------------------------------------------|--------------------------------------------------------|
| `models`           | 指定要使用的模型，以逗号分隔的列表形式。                     | `/?models=model1,model2`         |
| `model`            | 指定用于聊天会话的单一模型。                       | `/?model=model1`                 |
| `youtube`          | 指定要在聊天中转录的 YouTube 视频 ID。                 | `/?youtube=VIDEO_ID`             |
| `web-search`       | 如果设置为 `true`，则启用网络搜索功能。                              | `/?web-search=true`              |
| `tools` 或 `tool-ids` | 指定要激活的工具 ID 列表，以逗号分隔。          | `/?tools=tool1,tool2`            |
| `call`             | 如果设置为 `true`，则启用呼叫覆盖层。                                        | `/?call=true`                    |
| `q`                | 设置聊天的初始查询或提示。                                   | `/?q=Hello%20there`              |
| `temporary-chat`   | 如果设置为 `true`，则将聊天标记为临时会话，适用于一次性会话。            | `/?temporary-chat=true`          |

### 1. **模型和模型选择**

- **描述**: `models` 和 `model` 参数允许您指定在特定聊天会话中应使用哪些[语言模型](/features/workspace/models.md)。
- **设置方法**: 可以使用 `models` 来指定多个模型，或使用 `model` 来指定单一模型。
- **示例**:
  - `/?models=model1,model2` – 这将使用 `model1` 和 `model2` 初始化聊天。
  - `/?model=model1` – 这将 `model1` 设置为聊天的唯一模型。

### 2. **YouTube 转录**

- **描述**: `youtube` 参数接收一个 YouTube 视频 ID，使聊天能够转录指定的视频。
- **设置方法**: 使用 YouTube 视频 ID 作为该参数的值。
- **示例**: `/?youtube=VIDEO_ID`
- **功能**: 这会在聊天中触发对提供的 YouTube 视频进行转录的功能。

### 3. **网络搜索**

- **描述**: 启用 `web-search` 允许聊天会话访问[网络搜索](/tutorials/integrations/web_search)功能。
- **设置方法**: 将此参数设置为 `true` 以启用网络搜索。
- **示例**: `/?web-search=true`
- **功能**: 如果启用，聊天可以在其回复中检索网络搜索结果。

### 4. **工具选择**

- **描述**: `tools` 或 `tool-ids` 参数指定在聊天中激活哪些[工具](/features/plugin/tools)。
- **设置方法**: 提供以逗号分隔的工具 ID 列表作为参数的值。
- **示例**: `/?tools=tool1,tool2` 或 `/?tool-ids=tool1,tool2`
- **功能**: 每个工具 ID 都会在会话中匹配并激活，以便用户交互。

### 5. **通话覆盖**

- **描述**: 通过 call 参数，可以在聊天界面中添加视频或通话的覆盖层。
- **设置方法**: 将该参数设为 true 即可启用通话覆盖功能。
- **示例**: `/?call=true`
- **功能**: 激活呼叫界面覆盖层，允许实时转录和视频输入等功能。

### 6. **初始查询提示**

- **描述**: `q` 参数允许设置聊天的初始查询或提示。
- **设置方法**: 将查询或提示文本作为参数的值。
- **示例**: `/?q=Hello%20there`
- **功能**: 聊天将以指定的提示开始，并自动提交作为第一条消息。

### 7. **临时聊天会话**

- **描述**: `temporary-chat` 参数将聊天标记为临时会话。这可能会限制保存聊天历史记录或应用持久性设置等功能。
- **设置方法**: 将此参数设置为 `true` 以创建临时聊天会话。
- **示例**: `/?temporary-chat=true`
- **功能**: 这将启动一个不保存历史记录且不应用高级配置的临时聊天会话。

<details>
<summary>使用示例</summary>
:::tip **临时聊天会话**
假设用户希望发起一个不保存历史记录的快速聊天会话。他们可以通过在 URL 中设置 `temporary-chat=true` 来实现这一点。这提供了一个适合一次性互动的临时聊天环境。
:::
</details>

## 多个参数组合使用

这些 URL 参数可以组合使用，以创建高度定制化的聊天会话。例如：

```bash
/chat?models=model1,model2&youtube=VIDEO_ID&web-search=true&tools=tool1,tool2&call=true&q=Hello%20there&temporary-chat=true
```

这个 URL 将：

- 使用 `model1` 和 `model2` 初始化聊天。
- 启用 YouTube 转录、网络搜索和指定的工具。
- 显示呼叫覆盖层。
- 设置初始提示为 "Hello there"。
- 将聊天标记为临时，避免保存任何历史记录。