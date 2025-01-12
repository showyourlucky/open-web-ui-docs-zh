---
sidebar_position: 30
title: "🔗 Redis WebSocket 支持"
---

:::warning
此教程由社区贡献，未得到 OpenWebUI 团队的支持。它仅作为如何根据特定需求自定义 OpenWebUI 的示例。想要贡献？请查看贡献指南。
:::

# 🔗 Redis WebSocket 支持

## 概述

本文档页面概述了将 Redis 与 Open WebUI 集成以支持 WebSocket 所需的步骤。通过遵循这些步骤，您将能够为您的 Open WebUI 实例启用 WebSocket 功能，从而实现客户端与应用程序之间的实时通信和更新。

### 前提条件

* 一个有效的 Open WebUI 实例（运行版本 1.0 或更高）
* 一个 Redis 容器（在本例中我们将使用 `docker.io/valkey/valkey:8.0.1-alpine`，基于最新的 Redis 7.x 版本）
* 系统上安装了 Docker Compose（2.0 或更高版本）
* 用于 Open WebUI 和 Redis 之间通信的 Docker 网络
* 对 Docker、Redis 和 Open WebUI 的基本了解

## 设置 Redis

要为 WebSocket 支持设置 Redis，您需要创建一个包含以下内容的 `docker-compose.yml` 文件：

```yml
version: '3.9'
services:
  redis:
    image: docker.io/valkey/valkey:8.0.1-alpine
    container_name: redis-valkey
    volumes:
      - redis-data:/data
    command: "valkey-server --save 30 1"
    healthcheck:
      test: "[ $$(valkey-cli ping) = 'PONG' ]"
      start_period: 5s
      interval: 1s
      timeout: 3s
      retries: 5
    restart: unless-stopped
    cap_drop:
      - ALL
    cap_add:
      - SETGID
      - SETUID
      - DAC_OVERRIDE
    logging:
      driver: "json-file"
      options:
        max-size: "1m"
        max-file: "1"
    networks:
      - openwebui-network
```


volumes:
  redis-data:
```


networks:
  openwebui-network:
    external: true
```

:::info 注意事项

在此配置中未包含 `ports` 指令，因为在大多数情况下并不必要。Redis 服务仍可通过 Docker 网络被 Open WebUI 服务访问。然而，如果您需要从 Docker 网络外部访问 Redis 实例（例如，为了调试或监控），可以添加 `ports` 指令来暴露 Redis 端口（例如，`6379:6379`）。

上述配置设置了一个名为 `redis-valkey` 的 Redis 容器，并挂载了一个卷以确保数据持久化。`healthcheck` 指令确保如果容器未能响应 `ping` 命令，则会重启。`--save 30 1` 命令选项会在至少有一个键发生变化时，每 30 分钟将 Redis 数据库保存到磁盘。

:::

要创建一个用于 Open WebUI 和 Redis 之间通信的 Docker 网络，请运行以下命令：

```bash
docker network create openwebui-network
```

## 配置 Open WebUI

要在 Open WebUI 中启用 WebSocket 支持，您需要为您的 Open WebUI 实例设置以下环境变量：

```bash
ENABLE_WEBSOCKET_SUPPORT="true"
WEBSOCKET_MANAGER="redis"
WEBSOCKET_REDIS_URL="redis://redis:6379/1"
```

这些环境变量启用了 WebSocket 支持，指定了 Redis 作为 WebSocket 管理器，并定义了 Redis URL。请确保用实际的 Redis 实例 IP 地址替换 `WEBSOCKET_REDIS_URL` 的值。

当使用 Docker 运行 Open WebUI 时，您需要将其连接到相同的 Docker 网络：

```bash
docker run -d \
  --name open-webui \
  --network openwebui-network \
  -v open-webui:/app/backend/data \
  -e ENABLE_WEBSOCKET_SUPPORT="true" \
  -e WEBSOCKET_MANAGER="redis" \
  -e WEBSOCKET_REDIS_URL="redis://127.0.0.1:6379/1" \
  ghcr.io/open-webui/open-webui:main
```

请将 `127.0.0.1` 替换为 Docker 网络中 Redis 容器的实际 IP 地址。

## 验证

如果您正确设置了 Redis 并配置了 Open WebUI，在启动 Open WebUI 实例时应看到以下日志消息：

`DEBUG:open_webui.socket.main:Using Redis to manage websockets.`

这确认了 Open WebUI 正在使用 Redis 进行 WebSocket 管理。您也可以使用 `docker exec` 命令验证 Redis 实例是否正在运行并接受连接：

```bash
docker exec -it redis-valkey redis-cli -p 6379 ping
```

该命令应输出 `PONG`，表示 Redis 实例正常运行。如果此命令失败，您可以尝试以下命令：

```bash
docker exec -it redis-valkey valkey-cli -p 6379 ping
```

## 故障排除

如果您在 Redis 或 Open WebUI 的 WebSocket 支持方面遇到问题，可以参考以下资源进行故障排除：

* [Redis 文档](https://redis.io/docs)
* [Docker Compose 文档](https://docs.docker.com/compose/overview/)
* [sysctl 文档](https://man7.org/linux/man-pages/man8/sysctl.8.html)

通过遵循这些步骤和故障排除提示，您应该能够成功设置 Redis 与 Open WebUI 的集成，以支持 WebSocket，并实现实时通信和更新。