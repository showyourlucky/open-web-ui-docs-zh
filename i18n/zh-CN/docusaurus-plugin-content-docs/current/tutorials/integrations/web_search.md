---
sidebar_position: 5
title: "🌐 Web Search"
---

:::warning
本教程由社区贡献，并未得到Open WebUI团队的支持。它仅作为如何为您的特定用例自定义Open WebUI的演示。想要贡献？请查看贡献教程。
:::

## 🌐 网络搜索

本指南提供了如何在Open WebUI中使用各种搜索引擎设置网络搜索功能的说明。

## SearXNG (Docker)

> "**SearXNG是一款免费的互联网元搜索引擎，聚合来自各种搜索服务和数据库的结果。用户不会被追踪或分析。**"

## 1. SearXNG配置

要为Open WebUI优化配置SearXNG，请按照以下步骤操作：

**步骤1: `git clone` SearXNG Docker并导航到该文件夹：**

1. 创建一个新目录`searxng-docker`

 克隆searxng-docker仓库。此文件夹将包含您的SearXNG配置文件。有关配置说明，请参阅[SearXNG文档](https://docs.searxng.org/)。

```bash
git clone https://github.com/searxng/searxng-docker.git
```

导航到`searxng-docker`仓库：

```bash
cd searxng-docker
```

**步骤2: 定位并修改`.env`文件：**

1. 取消注释`.env`文件中的`SEARXNG_HOSTNAME`并进行相应设置：

```bash
# 默认监听https://localhost
# 要改变这个：
# * 取消注释SEARXNG_HOSTNAME，并将替换为SearXNG主机名
# * 取消注释LETSENCRYPT_EMAIL，并将替换为您的电子邮件（需要创建Let's Encrypt证书）

SEARXNG_HOSTNAME=localhost:8080/
# LETSENCRYPT_EMAIL=

# 可选：
# 如果您运行一个非常小或非常大的实例，您可能希望更改使用的uwsgi工作者数量和每个工作者的线程数量
# 更多的工作者（=进程）意味着可以同时处理更多的搜索请求，但也会导致更多的资源使用

# SEARXNG_UWSGI_WORKERS=4
# SEARXNG_UWSGI_THREADS=4
```

**步骤3: 修改`docker-compose.yaml`文件**

3. 通过修改`docker-compose.yaml`文件移除`localhost`限制：

```bash
sed -i "s/127.0.0.1:8080/0.0.0.0:8080/"
```

**步骤4: 授予必要的权限**

4. 通过在根目录运行以下命令，允许容器创建新的配置文件：

```bash
sudo chmod a+rwx searxng-docker/searxng
```

**步骤5: 创建一个非限制性的`limiter.toml`文件**

5. 创建一个非限制性的`searxng-docker/searxng/limiter.toml`配置文件：


searxng-docker/searxng/limiter.toml

```bash
# 此配置文件更新默认配置文件
# 请参阅https://github.com/searxng/searxng/blob/master/searx/botdetection/limiter.toml

[botdetection.ip_limit]
# 在ip_limit方法中激活link_token方法
link_token = false

[botdetection.ip_lists]
block_ip = []
pass_ip = []
```



**步骤6: 移除默认的`settings.yml`文件**

6. 如果存在，删除默认的`searxng-docker/searxng/settings.yml`文件，因为它将在SearXNG首次启动时重新生成：

```bash
rm searxng-docker/searxng/settings.yml
```

**步骤7: 创建一个新的`settings.yml`文件**

:::note
在第一次运行时，您必须从`docker-compose.yaml`文件中删除`cap_drop: - ALL`，以便`searxng`服务能够成功创建`/etc/searxng/uwsgi`.ini。这是必要的，因为`cap_drop: - ALL`指令会移除所有能力，包括创建`uwsgi.ini`文件所需的能力。首次运行后，出于安全原因，您应该重新添加`cap_drop: - ALL`到`docker-compose.yaml`文件中。
:::

7. 短暂启动容器以生成一个新的settings.yml文件：

```bash
docker compose up -d ; sleep 10 ; docker compose down
```

**步骤8: 添加格式并更新端口号**

8. 在`searxng-docker/searxng/settings.yml`文件中添加HTML和JSON格式：

```bash
sed -i 's/formats: \[\"html\"\/]/formats: [\"html\", \"json\"]/' searxng-docker/searxng/settings.yml
```

为您的SearXNG实例生成一个密钥：

```bash
sed -i "s|ultrasecretkey|$(openssl rand -hex 32)|g" searxng-docker/searxng/settings.yml
```

Windows用户可以使用以下powershell脚本生成密钥：

```powershell
$randomBytes = New-Object byte[] 32
(New-Object Security.Cryptography.RNGCryptoServiceProvider).GetBytes($randomBytes)
$secretKey = -join ($randomBytes | ForEach-Object { "{0:x2}" -f $_ })
(Get-Content searxng-docker/searxng/settings.yml) -replace 'ultrasecretkey', $secretKey | Set-Content searxng-docker/searxng/settings.yml
```

在`server`部分中更新端口号以匹配您之前设置的端口号（在本例中为`8080`）：

```bash
sed -i 's/port: 8080/port: 8080/' searxng-docker/searxng/settings.yml
```

根据需要更改`bind_address`：

```bash
sed -i 's/bind_address: "0.0.0.0"/bind_address: "127.0.0.1"/' searxng-docker/searxng/settings.yml
```

#### 配置文件

#### searxng-docker/searxng/settings.yml（摘要）

默认的`settings.yml`文件包含许多引擎设置。下面是默认`settings.yml`文件的一个摘录：


searxng-docker/searxng/settings.yml

```yaml
# 请参阅https://docs.searxng.org/admin/settings/settings.html#settings-use-default-settings
use_default_settings: true

server:
  # base_url在SEARXNG_BASE_URL环境变量中定义，请参阅.env和docker-compose.yml
  secret_key: "ultrasecretkey"  # 请更改此项！
  limiter: true  # 可以为私人实例禁用
  image_proxy: true
  port: 8080
  bind_address: "0.0.0.0"

ui:
  static_use_hash: true

search:
  safe_search: 0
  autocomplete: ""
  default_lang: ""
  formats:
    - html
    - json # 需要json
  # 移除格式以拒绝访问，请使用小写。
  # formats: [html, csv, json, rss]
redis:
  # 连接redis数据库的URL。会被${SEARXNG_REDIS_URL}覆盖。
  # https://docs.searxng.org/admin/settings/settings_redis.html#settings-redis
  url: redis://redis:6379/0
```

SearXNG的settings.yml文件中的端口应与您的docker-compose.yml文件中的端口号相匹配。



**步骤9: 更新`uwsgi.ini`文件**

9. 确保您的`searxng-docker/searxng/uwsgi.ini`文件与以下内容匹配：


searxng-docker/searxng/uwsgi.ini

```ini
[uwsgi]
# 谁将运行代码
uid = searxng
gid = searxng

# 工作者数量（通常为CPU数量）
# 默认值：%k（= CPU核心数量，请参阅Dockerfile）
workers = %k

# 每个工作者的线程数量
# 默认值：4（请参阅Dockerfile）
threads = 4

# 创建的套接字的权限
chmod-socket = 666

# 使用的插件和解释器配置
single-interpreter = true
master = true
plugin = python3
lazy-apps = true
enable-threads = 4

# 要导入的模块
module = searx.webapp

# 虚拟环境和python路径
pythonpath = /usr/local/searxng/
chdir = /usr/local/searxng/searx/

# 自动将进程名称设置为有意义的名字
auto-procname = true

# 出于隐私原因禁用请求日志
disable-logging = true
log-5xx = true

# 设置请求的最大大小（不包括请求主体）
buffer-size = 8192

# 无保持连接
# 请参阅https://github.com/searx/searx-docker/issues/24
add-header = Connection: close

# uwsgi为静态文件提供服务
static-map = /static=/usr/local/searxng/searx/static
# 过期时间设置为一天
static-expires = /* 86400
static-gzip-all = True
offload-threads = 4
```



## 2. 替代设置

或者，如果您不想修改默认配置，可以简单地创建一个空的`searxng-docker`文件夹并遵循其余的设置说明。

### Docker Compose设置

将以下环境变量添加到您的Open WebUI `docker-compose.yaml`文件中：

```yaml
services:
  open-webui:
    environment:
      ENABLE_RAG_WEB_SEARCH: True
      RAG_WEB_SEARCH_ENGINE: "searxng"
      RAG_WEB_SEARCH_RESULT_COUNT: 3
      RAG_WEB_SEARCH_CONCURRENT_REQUESTS: 10
      SEARXNG_QUERY_URL: "http://searxng:8080/search?q="
```

为SearXNG创建一个`.env`文件：

```
# SearXNG
SEARXNG_HOSTNAME=localhost:8080/
```

接下来，将以下内容添加到SearXNG的`docker-compose.yaml`文件中：

```yaml
services:
  searxng:
    container_name: searxng
    image: searxng/searxng:latest
    ports:
      - "8080:8080"
    volumes:
      - ./searxng:/etc/searxng:rw
    env_file:
      - .env
    restart: unless-stopped
    cap_drop:
      - ALL
    cap_add:
      - CHOWN
      - SETGID
      - SETUID
      - DAC_OVERRIDE
    logging:
      driver: "json-file"
      options:
        max-size: "1m"
        max-file: "1"
```

您的堆栈已准备好启动：

```bash
docker compose up -d
```

:::note
在第一次运行时，您必须从`docker-compose.yaml`文件中删除`cap_drop: - ALL`，以便`searxng`服务能够成功创建`/etc/searxng/uwsgi`.ini。这是必要的，因为`cap_drop: - ALL`指令会移除所有能力，包括创建`uwsgi.ini`文件所需的能力。首次运行后，出于安全原因，您应该重新添加`cap_drop: - ALL`到`docker-compose.yaml`文件中。
:::

或者，您可以直接使用`docker run`运行SearXNG：

```bash
docker run --name searxng --env-file .env -v ./searxng:/etc/searxng:rw -p 8080:8080 --restart unless-stopped --cap-drop ALL --cap-add CHOWN --cap-add SETGID --cap-add SETUID --cap-add DAC_OVERRIDE --log-driver json-file --log-opt max-size=1m,max-file=1 searxng/searxng:latest
```

## 3. 确认连接

在您的命令行界面中，从您的Open WebUI容器实例确认与SearXNG的连接：

```bash
docker exec -it open-webui curl http://host.docker.internal:8080/search?q=this+is+a+test+query&format=json
```

## 4. 图形界面配置

1. 导航至：`管理面板` -> `设置` -> `网络搜索`
2. 切换`启用网络搜索`
3. 从下拉菜单中将`搜索引擎`设置为`searxng`
4. 将`Searxng查询URL`设置为以下示例之一：

* `http://searxng:8080/search?q=`（使用容器名称和暴露的端口，适用于基于Docker的设置）
* `http://host.docker.internal:8080/search?q=`（使用`host.docker.internal` DNS名称和主机端口，适用于基于Docker的设置）
* `http:///search?q=`（使用本地域名，适用于本地网络访问）
* `https:///search?q=`（使用自定义域名的自托管SearXNG实例，适用于公共或私人访问）

**请注意，`/search?q=`部分是必需的。**
5. 根据需要调整`搜索结果数量`和`并发请求`的值
6. 保存更改

![SearXNG图形界面配置](/img/tutorial_searxng_config.png)

## 5. 在聊天中使用网络搜索

要访问网络搜索，请点击消息输入字段旁边的+。

在这里，您可以切换网络搜索开启/关闭。

![网络搜索用户界面切换](/img/web_search_toggle.png)

通过遵循这些步骤，您将成功设置SearXNG与Open WebUI的集成，使您能够使用SearXNG引擎进行网络搜索。

#### 注意

您必须在聊天中明确地切换此项开启/关闭。

这是基于每个会话启用的，例如重新加载页面，切换到另一个聊天将关闭。

## Google PSE API

### 设置

1. 前往Google Developers，使用[可编程搜索引擎](https://developers.google.com/custom-search)，登录或创建账户。
2. 前往[控制面板](https://programmablesearchengine.google.com/controlpanel/all)并点击`添加`按钮
3. 输入搜索引擎名称，设置其他属性以满足您的需求，验证您不是机器人并点击`创建`按钮。
4. 生成`API密钥`并获取`搜索引擎ID`。（在创建引擎后可用）
5. 使用`API密钥`和`搜索引擎ID`，打开`Open WebUI管理面板`并点击`设置`选项卡，然后点击`网络搜索`
6. 启用`网络搜索`并将`搜索引擎`设置为`google_pse`
7. 用`API密钥`填写`Google PSE API Key`，并用`搜索引擎ID`填写`Google PSE Engine Id`（第4步）
8. 点击`保存`

![Open WebUI管理面板](/img/tutorial_google_pse1.png)

#### 注意

您必须在提示字段中使用加号（`+`）按钮启用`网络搜索`。
搜索网络;-)

![启用网络搜索](/img/tutorial_google_pse2.png)

## Brave API

### Docker Compose设置

将以下环境变量添加到您的Open WebUI `docker-compose.yaml`文件中：

```yaml
services:
  open-webui:
    environment:
      ENABLE_RAG_WEB_SEARCH: True
      RAG_WEB_SEARCH_ENGINE: "brave"
      BRAVE_SEARCH_API_KEY: "YOUR_API_KEY"
      RAG_WEB_SEARCH_RESULT_COUNT: 3
      RAG_WEB_SEARCH_CONCURRENT_REQUESTS: 10
```

## Mojeek Search API

### 设置

1. 请访问[Mojeek Search API页面](https://www.mojeek.com/services/search/web-search-api/)以获取`API密钥`
2. 使用`API密钥`，打开`Open WebUI管理面板`并点击`设置`选项卡，然后点击`网络搜索`
3. 启用`网络搜索`并将`搜索引擎`设置为`mojeek`
4. 用`API密钥`填写`Mojeek Search API Key`
5. 点击`保存`

### Docker Compose设置

将以下环境变量添加到您的Open WebUI `docker-compose.yaml`文件中：

```yaml
services:
  open-webui:
    environment:
      ENABLE_RAG_WEB_SEARCH: True
      RAG_WEB_SEARCH_ENGINE: "mojeek"
      BRAVE_SEARCH_API_KEY: "YOUR_MOJEEK_API_KEY"
      RAG_WEB_SEARCH_RESULT_COUNT: 3
      RAG_WEB_SEARCH_CONCURRENT_REQUESTS: 10
```

## SearchApi API

[SearchApi](https://searchapi.io)是一个实时SERP API集合。任何现有或即将推出的返回`organic_results`的SERP引擎都受支持。默认的网络搜索引擎是`google`，但可以更改为`bing`，`baidu`，`google_news`，`bing_news`，`google_scholar`，`google_patents`等。

### 设置

1. 前往[SearchApi](https://searchapi.io)，登录或创建一个新账户。
2. 前往`仪表板`并复制API密钥。
3. 使用`API密钥`，打开`Open WebUI管理面板`并点击`设置`选项卡，然后点击`网络搜索`。
4. 启用`网络搜索`并将`搜索引擎`设置为`searchapi`。
5. 用您从[SearchApi](https://www.searchapi.io/)仪表板第2步复制的`API密钥`填写`SearchApi API Key`。
6. [可选] 输入您想查询的`SearchApi引擎`名称。例如，`google`，`bing`，`baidu`，`google_news`，`bing_news`，`google_videos`，`google_scholar`和`google_patents`。默认设置为`google`。
7. 点击`保存`。

![Open WebUI管理面板](/img/tutorial_searchapi_search.png)

#### 注意

您必须在提示字段中使用加号（`+`）按钮启用`网络搜索`，以使用[SearchApi](https://www.searchapi.io/)引擎搜索网络。

![启用网络搜索](/img/enable_web_search.png)

## Kagi API

即将推出

### 设置

## Serpstack API

即将推出

### 设置

## Serper API

即将推出

### 设置

## Serply API

即将推出

### 设置

## DuckDuckGo API

### 设置

使用DuckDuckGo API进行Open WebUI内置网络搜索无需设置！DuckDuckGo在Open WebUI中开箱即用。

:::note
您的网络搜索可能会受到限流。
:::

## Tavily API

即将推出

### 设置

## Jina API

即将推出

### 设置

## Bing API

### 设置

1. 导航到[AzurePortal](https://portal.azure.com/#create/Microsoft.BingSearch)并创建一个新资源。创建后，您将被重定向到资源概览页面。从那里选择"点击此处管理密钥。" ![点击此处管理密钥](https://github.com/user-attachments/assets/dd2a3c67-d6a7-4198-ba54-67a3c8acff6d)
2. 在密钥管理页面，找到Key1或Key2并复制您想要的密钥。
3. 打开Open WebUI管理面板，切换到设置选项卡，然后选择网络搜索。
4. 启用网络搜索选项并将搜索引擎设置为bing。
5. 用您从[AzurePortal](https://portal.azure.com/#create/Microsoft.BingSearch)仪表板第2步复制的`API密钥`填写`SearchApi API Key`。
6. 点击`保存`。

