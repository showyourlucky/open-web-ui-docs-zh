---
sidebar_position: 14
title: "🛃 使用自定义 CA 存储进行设置"
---


:::warning
此教程由社区贡献，不受到 OpenWebUI 团队的支持。它仅作为如何根据特定使用场景定制 OpenWebUI 的示例。想要贡献？请参阅贡献指南。
:::

如果你在运行 OI 时遇到 `[SSL: CERTIFICATE_VERIFY_FAILED]` 错误，很可能是因为你所在的网络会拦截 HTTPS 流量（例如公司网络）。

要解决这个问题，你需要将新的证书添加到 OI 的信任存储中。

**对于预构建的 Docker 镜像**：

1. 通过传递 `--volume=/etc/ssl/certs/ca-certificiate.crt:/etc/ssl/certs/ca-certificates.crt:ro` 参数给 `docker run` 命令，从主机挂载证书存储到容器中。
2. 通过设置 `REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` 强制 Python 使用系统信任存储（详见 [Docker 文档](https://docs.docker.com/reference/cli/docker/container/run/#env)）。

如果环境变量 `REQUESTS_CA_BUNDLE` 不起作用，可以尝试设置 `SSL_CERT_FILE`（参考 [httpx 文档](https://www.python-httpx.org/environment_variables/#ssl_cert_file)），并赋予相同的值。

这是 [@KizzyCode](https://github.com/open-webui/open-webui/issues/1398#issuecomment-2258463210) 提供的一个 `compose.yaml` 示例：

```yaml
services:
  openwebui:
    image: ghcr.io/open-webui/open-webui:main
    volumes:
      - /var/containers/openwebui:/app/backend/data:rw
      - /etc/containers/openwebui/compusrv.crt:/etc/ssl/certs/ca-certificates.crt:ro
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    environment:
      - WEBUI_NAME=compusrv
      - ENABLE_SIGNUP=False
      - ENABLE_COMMUNITY_SHARING=False
      - WEBUI_SESSION_COOKIE_SAME_SITE=strict
      - WEBUI_SESSION_COOKIE_SECURE=True
      - ENABLE_OLLAMA_API=False
      - REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt
```

`ro` 标志将 CA 存储以只读方式挂载，防止意外修改主机上的 CA 存储。
**对于本地开发**：

你也可以在构建过程中通过修改 `Dockerfile` 添加证书。这对于想要对用户界面进行修改的情况非常有用。
由于构建过程分为多个阶段（参见 [Docker 多阶段构建](https://docs.docker.com/build/building/multi-stage/)），你需要在每个阶段都添加证书。

1. 前端 (`build` 阶段)：

```dockerfile
COPY package.json package-lock.json <YourRootCert>.crt ./
ENV NODE_EXTRA_CA_CERTS=/app/<YourRootCert>.crt
RUN npm ci
```

2. 后端 (`base` 阶段)：

```dockerfile
COPY <CorporateSSL.crt> /usr/local/share/ca-certificates/
RUN update-ca-certificates
ENV PIP_CERT=/etc/ssl/certs/ca-certificates.crt \
    REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt
```