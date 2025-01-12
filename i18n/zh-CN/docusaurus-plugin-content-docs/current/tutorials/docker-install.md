---
sidebar_position: 0
title: 🐳 Docker 安装指南
---

:::warning
此教程由社区贡献，并未得到 OpenWebUI 团队的支持。它仅作为如何根据特定需求定制 OpenWebUI 的示例。想要贡献？请参阅贡献指南。
:::

# 安装 Docker

## Windows 和 Mac 用户

- 从 [Docker 官方网站](https://www.docker.com/products/docker-desktop) 下载 Docker Desktop。  
- 按照网站上的安装说明进行操作。  
- 安装完成后，**打开 Docker Desktop** 确认其正常运行。

---

## Ubuntu 用户

1. **打开终端。**

2. **设置 Docker 的 apt 仓库：**

   ```bash
   sudo apt-get update
   sudo apt-get install ca-certificates curl
   sudo install -m 0755 -d /etc/apt/keyrings
   sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
   sudo chmod a+r /etc/apt/keyrings/docker.asc
   echo \
     "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
     $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
     sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

:::note
如果你使用的是 **Ubuntu 衍生版**（如 Linux Mint），请使用 `UBUNTU_CODENAME` 而不是 `VERSION_CODENAME`。
:::

3. **安装 Docker 引擎：**

   ```bash
   sudo apt-get update
   sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
   ```

4. **验证 Docker 安装：**

   ```bash
   sudo docker run hello-world
   ```

---

## 其他 Linux 发行版

对于其他 Linux 发行版，请参考 [Docker 官方文档](https://docs.docker.com/engine/install/)。

---

## 安装并验证 Ollama

1. **从 [https://ollama.com/](https://ollama.com/) 下载 Ollama**。

2. **验证 Ollama 安装：**
   - 打开浏览器并导航至：
     [http://127.0.0.1:11434/](http://127.0.0.1:11434/)。
   - 注意：端口号可能会因你的安装情况而有所不同。
