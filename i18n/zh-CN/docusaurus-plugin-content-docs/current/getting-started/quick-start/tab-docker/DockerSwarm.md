## Docker Swarm

此安装方法需要了解 Docker Swarm 的相关知识，因为它使用一个堆栈文件在 Docker Swarm 中部署三个独立的服务容器。

它包含了独立的 ChromaDB、Ollama 和 OpenWebUI 容器。
此外，还提供了预填充的[环境变量](../advanced-topics/env-configuration)，以进一步说明设置过程。

根据您的硬件配置选择合适的命令：

- **开始之前**：

  需要在主机上创建用于卷的目录，或者您可以指定自定义位置或卷。

  当前示例使用了一个隔离的 `data` 目录，该目录与 `docker-stack.yaml` 文件位于同一目录下。

      - **例如**：

        ```bash
        mkdir -p data/open-webui data/chromadb data/ollama
        ```

- **支持 GPU**：

  #### docker-stack.yaml

    ```yaml
    version: '3.9'

    services:
      openWebUI:
        image: ghcr.io/open-webui/open-webui:main
        depends_on:
            - chromadb
            - ollama
        volumes:
          - ./data/open-webui:/app/backend/data
        environment:
          DATA_DIR: /app/backend/data 
          OLLAMA_BASE_URLS: http://ollama:11434
          CHROMA_HTTP_PORT: 8000
          CHROMA_HTTP_HOST: chromadb
          CHROMA_TENANT: default_tenant
          VECTOR_DB: chroma
          WEBUI_NAME: Awesome ChatBot
          CORS_ALLOW_ORIGIN: "*" # 这是当前默认值，在上线前需要修改
          RAG_EMBEDDING_ENGINE: ollama
          RAG_EMBEDDING_MODEL: nomic-embed-text-v1.5
          RAG_EMBEDDING_MODEL_TRUST_REMOTE_CODE: "True"
        ports:
          - target: 8080
            published: 8080
            mode: overlay
        deploy:
          replicas: 1
          restart_policy:
            condition: any
            delay: 5s
            max_attempts: 3

      chromadb:
        hostname: chromadb
        image: chromadb/chroma:0.5.15
        volumes:
          - ./data/chromadb:/chroma/chroma
        environment:
          - IS_PERSISTENT=TRUE
          - ALLOW_RESET=TRUE
          - PERSIST_DIRECTORY=/chroma/chroma
        ports: 
          - target: 8000
            published: 8000
            mode: overlay
        deploy:
          replicas: 1
          restart_policy:
            condition: any
            delay: 5s
            max_attempts: 3
        healthcheck: 
          test: ["CMD-SHELL", "curl localhost:8000/api/v1/heartbeat || exit 1"]
          interval: 10s
          retries: 2
          start_period: 5s
          timeout: 10s

      ollama:
        image: ollama/ollama:latest
        hostname: ollama
        ports:
          - target: 11434
            published: 11434
            mode: overlay
        deploy:
          resources:
            reservations:
              generic_resources:
                - discrete_resource_spec:
                    kind: "NVIDIA-GPU"
                    value: 0
          replicas: 1
          restart_policy:
            condition: any
            delay: 5s
            max_attempts: 3
        volumes:
          - ./data/ollama:/root/.ollama
    ```

  - **额外要求**：

      1. 确保启用了 CUDA，并按照您操作系统和 GPU 的说明进行操作。
      2. 启用 Docker GPU 支持，请参阅 [Nvidia Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html " 在 Nvidia 的网站上")。
      3. 按照[这里的指南配置 Docker Swarm 以支持您的 GPU](https://gist.github.com/tomlankhorst/33da3c4b9edbde5c83fc1244f010815c#configuring-docker-to-work-with-your-gpus)。
    - 确保在 `/etc/nvidia-container-runtime/config.toml` 中启用了 _GPU 资源_，并取消注释 `swarm-resource = "DOCKER_RESOURCE_GPU"`。每次更新这些文件后，必须重启每个节点上的 Docker 守护进程。

- **仅支持 CPU**：

    修改 `docker-stack.yaml` 中的 Ollama 服务，并删除 `generic_resources:` 相关的行。

    ```yaml
    ollama:
      image: ollama/ollama:latest
      hostname: ollama
      ports:
        - target: 11434
          published: 11434
          mode: overlay
      deploy:
        replicas: 1
        restart_policy:
          condition: any
          delay: 5s
          max_attempts: 3
      volumes:
        - ./data/ollama:/root/.ollama
    ```

- **部署 Docker 堆栈**：

  ```bash
  docker stack deploy -c docker-stack.yaml -d super-awesome-ai
  ```
