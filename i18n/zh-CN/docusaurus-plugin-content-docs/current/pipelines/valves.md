---
sidebar_position: 3
title: "⚙️ 阀门"
---

# 阀门

阀门是按管道设置的输入变量。它们作为 `Pipeline` 类的子类进行定义，并在 `Pipeline` 类的 `__init__` 方法中初始化。

在向管道添加阀门时，应包含一种机制，使管理员能够在 Web 界面中重新配置这些阀门。以下是几种实现方式：

- 使用 `os.getenv()` 来设置环境变量，以便在管道中使用，并提供一个默认值以防环境变量未被设置。示例如下：

```python
self.valves = self.Valves(
    **{
        "LLAMAINDEX_OLLAMA_BASE_URL": os.getenv("LLAMAINDEX_OLLAMA_BASE_URL", "http://localhost:11434"),
        "LLAMAINDEX_MODEL_NAME": os.getenv("LLAMAINDEX_MODEL_NAME", "llama3"),
        "LLAMAINDEX_EMBEDDING_MODEL_NAME": os.getenv("LLAMAINDEX_EMBEDDING_MODEL_NAME", "nomic-embed-text"),
    }
)
```

- 将阀门设置为 `Optional` 类型，这样即使没有为阀门设置任何值，管道仍然可以加载。

```python
class Pipeline:
    class Valves(BaseModel):
        target_user_roles: List[str] = ["user"]
        max_turns: Optional[int] = None
```

如果你没有提供一种在 Web 界面中更新阀门的方法，在尝试将管道添加到 Web 界面后，你将在管道服务器日志中看到如下错误信息：
`WARNING:root:未找到 <pipeline name> 中的 Pipeline 类`
