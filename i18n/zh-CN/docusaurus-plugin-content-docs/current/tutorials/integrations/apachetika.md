---
sidebar_position: 4000
title: "🪶 Apache Tika 提取"
---

:::warning
本教程由社区贡献，未得到 OpenWebUI 团队的支持。它仅作为如何根据具体需求自定义 OpenWebUI 的示例。想要贡献？请查看贡献指南。
:::

## 🪶 Apache Tika 提取

本文档提供了一步步将 Apache Tika 与 Open WebUI 集成的指南。Apache Tika 是一个内容分析工具包，可以用于检测和提取超过一千种不同文件类型的元数据和文本内容。所有这些文件类型都可以通过单一接口进行解析，使得 Tika 在搜索引擎索引、内容分析、翻译等方面非常有用。

### 前提条件
------------

* Open WebUI 实例
* 系统中已安装 Docker
* 已为 Open WebUI 设置 Docker 网络

### 集成步骤
----------------

#### 第一步：创建 Docker Compose 文件或运行 Apache Tika 的 Docker 命令

你有两种方式来运行 Apache Tika：

**选项一：使用 Docker Compose**

在同一目录下创建一个名为 `docker-compose.yml` 的新文件，并将以下配置添加到该文件中：

```yml
services:
  tika:
    image: apache/tika:latest-full
    container_name: tika
    ports:
      - "9998:9998"
    restart: unless-stopped
```

使用以下命令运行 Docker Compose 文件：

```bash
docker-compose up -d
```

**选项二：使用 Docker Run 命令**

或者，你可以使用以下 Docker 命令运行 Apache Tika：

```bash
docker run -d --name tika \
  -p 9998:9998 \
  -restart unless-stopped \
  apache/tika:latest-full
```

请注意，如果你选择使用 Docker run 命令，则需要指定 `--network` 标志以确保容器在与 Open WebUI 实例相同的网络中运行。

#### 第二步：配置 Open WebUI 使用 Apache Tika

要在 Open WebUI 中使用 Apache Tika 作为上下文提取引擎，请按照以下步骤操作：

* 登录到你的 Open WebUI 实例。
* 导航至 `Admin Panel` 设置菜单。
* 点击 `Settings`。
* 点击 `Documents` 标签。
* 将 `Default` 内容提取引擎下拉菜单更改为 `Tika`。
* 更新上下文提取引擎 URL 为 `http://tika:9998`。
* 保存更改。

### 验证 Docker 环境中的 Apache Tika
=====================================

要验证 Apache Tika 是否在 Docker 环境中正常工作，可以按照以下步骤操作：

#### 1. 启动 Apache Tika Docker 容器

首先，确保 Apache Tika Docker 容器正在运行。你可以使用以下命令启动它：

```bash
docker run -p 9998:9998 apache/tika
```

此命令启动 Apache Tika 容器并将容器的 9998 端口映射到本地机器的 9998 端口。

#### 2. 验证服务器是否运行

你可以通过发送 GET 请求来验证 Apache Tika 服务器是否正常运行：

```bash
curl -X GET http://localhost:9998/tika
```

此命令应返回以下响应：

```
This is Tika Server. Please PUT
```

#### 3. 验证集成

你也可以尝试发送文件进行分析以测试集成。通过 `curl` 命令测试 Apache Tika：

```bash
curl -T test.txt http://localhost:9998/tika
```

将 `test.txt` 替换为你本地机器上的文本文件路径。

Apache Tika 将响应文件的检测元数据和内容类型。

### 使用脚本验证 Apache Tika

如果你想自动化验证过程，这个脚本会将文件发送给 Apache Tika 并检查响应中的预期元数据。如果元数据存在，脚本将输出成功消息及文件的元数据；否则，将输出错误消息及来自 Apache Tika 的响应。

```python
import requests

def verify_tika(file_path, tika_url):
    try:
        # 发送文件到 Apache Tika 并验证输出
        response = requests.put(tika_url, files={'file': open(file_path, 'rb')})

        if response.status_code == 200:
            print("Apache Tika 成功分析了文件。")
            print("来自 Apache Tika 的响应：")
            print(response.text)
        else:
            print("分析文件时出错：")
            print(f"状态码: {response.status_code}")
            print(f"来自 Apache Tika 的响应: {response.text}")
    except Exception as e:
        print(f"发生错误: {e}")

if __name__ == "__main__":
    file_path = "test.txt"  # 替换为你想发送给 Apache Tika 的文件路径
    tika_url = "http://localhost:9998/tika"

    verify_tika(file_path, tika_url)
```

### 运行脚本的说明

#### 前提条件

* 系统中必须安装 Python 3.x
* 必须安装 `requests` 库（可以通过 pip 安装：`pip install requests`）
* Apache Tika Docker 容器必须运行（使用 `docker run -p 9998:9998 apache/tika` 命令）
* 将 `"test.txt"` 替换为你想发送给 Apache Tika 的文件路径

#### 运行脚本

1. 将脚本保存为 `verify_tika.py`（例如，使用文本编辑器如 Notepad 或 Sublime Text）
2. 打开终端或命令提示符
3. 导航到保存脚本的目录（使用 `cd` 命令）
4. 使用以下命令运行脚本：`python verify_tika.py`
5. 脚本将输出一条消息，指示 Apache Tika 是否正常工作

注意：如果遇到任何问题，请确保 Apache Tika 容器正确运行且文件发送到了正确的 URL。

### 结论

通过遵循这些步骤，你可以验证 Apache Tika 是否在 Docker 环境中正常工作。你可以通过发送文件进行分析、使用 GET 请求验证服务器是否运行，或者使用脚本来自动化这一过程来进行测试。如果遇到任何问题，请确保 Apache Tika 容器正确运行且文件发送到了正确的 URL。

### 故障排除
--------------

* 确保 Apache Tika 服务正在运行并可从 Open WebUI 实例访问。
* 检查 Docker 日志中是否有与 Apache Tika 服务相关的错误或问题。
* 验证 Open WebUI 中的上下文提取引擎 URL 是否正确配置。

### 集成的好处
----------------------

将 Apache Tika 与 Open WebUI 集成提供了多个好处，包括：

* **改进的元数据提取**：Apache Tika 的高级元数据提取功能可以帮助你准确提取文件中的相关数据。
* **支持多种文件格式**：Apache Tika 支持广泛的文件格式，是处理多样化文件类型的理想解决方案。
* **增强的内容分析**：Apache Tika 的高级内容分析功能可以帮助你从文件中提取有价值的信息。

### 结论
----------

将 Apache Tika 与 Open WebUI 集成是一个简单的过程，可以提升 Open WebUI 实例的元数据提取能力。通过遵循本文档中的步骤，你可以轻松设置 Apache Tika 作为 Open WebUI 的上下文提取引擎。
