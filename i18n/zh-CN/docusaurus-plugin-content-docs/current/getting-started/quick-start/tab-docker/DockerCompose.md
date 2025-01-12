# Docker Compose 设置

使用 Docker Compose 可以简化多容器 Docker 应用的管理。

如果你还没有安装 Docker，请参阅我们的 [Docker 安装教程](docs/tutorials/docker-install.md)。

Docker Compose 需要一个额外的包，即 `docker-compose-v2`。

**警告：** 较旧的 Docker Compose 教程可能会引用版本 1 的语法，这些语法使用诸如 `docker-compose build` 这样的命令。请确保你使用的是版本 2 的语法，它使用诸如 `docker compose build`（注意这里用空格代替了连字符）这样的命令。

## 示例 `docker-compose.yml`

以下是一个使用 Docker Compose 设置 Open WebUI 的示例配置文件：

```yaml
version: '3'
services:
  openwebui:
    image: ghcr.io/open-webui/open-webui:main
    ports:
      - "3000:8080"
    volumes:
      - open-webui:/app/backend/data
volumes:
  open-webui:
```

## 启动服务

要启动你的服务，请运行以下命令：

```bash
docker compose up -d
```

## 辅助脚本

代码库中包含了一个名为 `run-compose.sh` 的实用辅助脚本。这个脚本可以帮助你在部署时选择要包含的 Docker Compose 文件，从而简化设置过程。

---

**注意：** 若要支持 Nvidia GPU，在你的 `docker-compose.yml` 文件的服务定义中添加以下内容：

```yaml
deploy:
  resources:
    reservations:
      devices:
        - driver: nvidia
          count: all
          capabilities: [gpu]
```

这种设置确保你的应用程序在有可用 GPU 资源时可以充分利用它们。