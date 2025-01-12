---
sidebar_position: 2
title: "🐍 Python 代码执行"
---

# 🐍 Python 代码执行

## 概述

Open WebUI 支持在浏览器中客户端执行 Python 代码，通过 Pyodide 在聊天中的代码块内运行脚本。这一功能使得大型语言模型（LLMs）能够生成可以直接在浏览器中执行的 Python 脚本，并利用 Pyodide 支持的各种库。

为了保护用户隐私并提供灵活性，Open WebUI 镜像了 PyPI 包，避免直接进行外部网络请求。这种方法还允许在没有互联网连接的环境中使用 Pyodide。

Open WebUI 前端包含一个自包含的 WASM（WebAssembly）Python 环境，由 Pyodide 提供支持，可以执行 LLMs 生成的基本 Python 脚本。此环境设计得非常易于使用，无需额外设置或安装。

## 支持的库

Pyodide 代码执行配置为仅加载在 scripts/prepare-pyodide.js 中配置并在 "CodeBlock.svelte" 中添加的包。目前 Open WebUI 支持以下 Pyodide 包：

* micropip
* packaging
* requests
* beautifulsoup4
* numpy
* pandas
* matplotlib
* scikit-learn
* scipy
* regex

这些库可用于执行各种任务，如数据处理、机器学习和网页抓取。如果你希望使用的包未包含在我们提供的 Pyodide 版本中，则无法使用该包。

## 调用 Python 代码执行

要执行 Python 代码，请在聊天中要求一个 LLM 为你编写一个 Python 脚本。一旦 LLM 生成了代码，在代码块的右上角会出现一个“运行”按钮。点击此按钮将使用 Pyodide 执行代码。要在代码块底部显示结果，请确保代码中至少有一个 print 语句以显示结果。

## 使用 Python 代码执行的技巧

* 编写 Python 代码时，请记住代码将在 Pyodide 环境中执行。你可以在请求代码时提到“Pyodide 环境”来告知 LLM。
* 查阅 Pyodide 文档以了解该环境的功能和限制。
* 尝试不同的库和脚本，探索在 Open WebUI 中执行 Python 代码的可能性。

## Pyodide 文档

* [Pyodide 文档](https://pyodide.org/en/stable/)

## 代码示例

以下是一个可以使用 Pyodide 执行的简单 Python 脚本示例：

```python
import pandas as pd

# 创建示例 DataFrame
data = {'Name': ['John', 'Anna', 'Peter'], 
        'Age': [28, 24, 35]}
df = pd.DataFrame(data)

# 打印 DataFrame
print(df)
```

这个脚本将使用 pandas 创建一个示例 DataFrame，并在你的聊天窗口中的代码块下方打印出来。

## 扩展支持的库列表

想要突破现有功能的界限吗？要扩展支持的库列表，请按照以下步骤操作：

1. **Fork the Pyodide repository**（分叉 Pyodide 仓库）以创建自己的版本。
2. **选择新包**，可以从现有的 Pyodide 包中选择，或者探索 Open WebUI 当前缺少的高质量包。
3. **将新包集成**到你分叉的仓库中，解锁更多可能性。
