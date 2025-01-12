---
sidebar_position: 7
title: "🗄️ 分别托管 UI 和模型"
---

:::warning
此教程由社区贡献，并未得到 OpenWebUI 团队的支持。它仅作为如何根据特定需求自定义 OpenWebUI 的示例。想要贡献？请查看贡献教程。
:::

:::note
如果你计划将此服务暴露到广域网，请考虑实施安全措施，如 [网络防火墙](https://github.com/chr0mag/geoipsets)、[Web 应用防火墙](https://github.com/owasp-modsecurity/ModSecurity) 和 [威胁情报](https://github.com/crowdsecurity/crowdsec)。
此外，强烈建议在你的 **HTTPS** 配置中启用 HSTS，例如 `Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"`，并在你的 **HTTP** 配置中设置某种重定向到你的 **HTTPS URL**。对于免费的 SSL 证书，[Let's Encrypt](https://letsencrypt.org/) 是一个不错的选择，并且可以结合 [Certbot](https://github.com/certbot/certbot) 进行管理。
:::

有时，将 Ollama 与 UI 分开托管是有益的，但仍保留跨用户共享的 RAG 和 RBAC 支持功能：

## UI 配置

对于 UI 配置，你可以按如下方式设置 Apache VirtualHost：

```bash
# 假设你有一个网站托管此 UI 在 "server.com"
<VirtualHost 192.168.1.100:80>
    ServerName server.com
    DocumentRoot /home/server/public_html
    ProxyPass / http://server.com:3000/ nocanon
    ProxyPassReverse / http://server.com:3000/
    RewriteEngine on
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{HTTP:Connection} upgrade [NC]
    RewriteRule ^/?(.*) "ws://server.com:3000/$1" [P,L]
</VirtualHost>
```

在请求 SSL 之前，请先启用该站点：

:::warning
使用 `nocanon` 选项可能会影响后端的安全性。只有在配置需要时才建议启用此选项。

通常，mod_proxy 会规范 ProxyPassed URLs。但这可能会与某些后端不兼容，特别是那些使用 PATH_INFO 的后端。可选的 nocanon 关键字会抑制这种行为，并将 URL 路径“原始”传递给后端。请注意，此关键字可能会影响后端的安全性，因为它移除了代理提供的对基于 URL 攻击的有限保护。
:::

```bash
a2ensite server.com.conf  # 这将启用站点。a2ensite 是 "Apache 2 Enable Site" 的缩写
```

```bash
# 对于 SSL
<VirtualHost 192.168.1.100:443>
    ServerName server.com
    DocumentRoot /home/server/public_html
    ProxyPass / http://server.com:3000/ nocanon
    ProxyPassReverse / http://server.com:3000/
    RewriteEngine on
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{HTTP:Connection} upgrade [NC]
    RewriteRule ^/?(.*) "ws://server.com:3000/$1" [P,L]
    SSLEngine on
    SSLCertificateFile /etc/ssl/virtualmin/170514456861234/ssl.cert
    SSLCertificateKeyFile /etc/ssl/virtualmin/170514456861234/ssl.key
    SSLProtocol all -SSLv2 -SSLv3 -TLSv1 -TLSv1.1
    SSLProxyEngine on
    SSLCACertificateFile /etc/ssl/virtualmin/170514456865864/ssl.ca
</VirtualHost>
```

这里我使用 Virtualmin 来管理我的 SSL 集群，但你也可以直接使用 Certbot 或其他你喜欢的 SSL 方法。要使用 SSL：

### 前提条件

运行以下命令：

```bash
snap install certbot --classic
snap apt install python3-certbot-apache  # 安装 apache 插件
```

导航到 apache sites-available 目录：

```bash
cd /etc/apache2/sites-available/
```

创建 `server.com.conf` 文件（如果尚未创建），包含上述 `<virtualhost>` 配置（应匹配你的实际情况，必要时进行修改）。使用没有 SSL 的版本：

一旦创建完成，运行 `certbot --apache -d server.com`，这将为你请求并添加/创建 SSL 密钥，同时创建 `server.com.le-ssl.conf`。

# 配置 Ollama 服务器

在最新安装的 Ollama 中，确保你已经按照官方 Ollama 参考设置了 API 服务器：

[Ollama FAQ](https://github.com/jmorganca/ollama/blob/main/docs/faq.md)

### 简要说明

指南似乎与当前更新的服务文件不匹配。因此，我们在这里解决这个问题：

除非你从源代码编译 Ollama，标准安装 `curl https://ollama.com/install.sh | sh` 会在 `/etc/systemd/system` 目录下创建一个名为 `ollama.service` 的文件。你可以使用 nano 编辑该文件：

```bash
sudo nano /etc/systemd/system/ollama.service
```

添加以下行：

```ini
Environment="OLLAMA_HOST=0.0.0.0:11434"  # 此行是必需的。你也可以指定其他端口
```

例如：

```ini
[Unit]
Description=Ollama Service
After=network-online.target

[Service]
ExecStart=/usr/local/bin/ollama serve
Environment="OLLAMA_HOST=0.0.0.0:11434"  # 此行是必需的。你也可以指定其他 IP 和端口
Environment="OLLAMA_ORIGINS=http://192.168.254.106:11434,https://models.server.city"  # 此行是可选的
User=ollama
Group=ollama
Restart=always
RestartSec=3
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/s>

[Install]
WantedBy=default.target
```

保存文件：按 CTRL+S，然后按 CTRL+X。

当你的计算机重启时，Ollama 服务器将在你指定的 IP:PORT 上监听，例如 0.0.0.0:11434 或 192.168.254.106:11434（取决于你的本地 IP 地址）。确保路由器正确配置以通过转发 11434 端口来提供页面。

# Ollama 模型配置

## 对于 Ollama 模型配置，使用以下 Apache VirtualHost 设置

导航到 apache sites-available 目录：

```bash
cd /etc/apache2/sites-available/
```

```bash
nano models.server.city.conf  # 与你的 Ollama 服务器域名匹配
```

添加以下 VirtualHost 示例（根据需要修改）：

```bash
# 假设你有一个网站托管此 UI 在 "models.server.city"
<IfModule mod_ssl.c>
    <VirtualHost 192.168.254.109:443>
        DocumentRoot "/var/www/html/"
        ServerName models.server.city
        <Directory "/var/www/html/">
            Options None
            Require all granted
        </Directory>
        ProxyRequests Off
        ProxyPreserveHost On
        ProxyAddHeaders On
        SSLProxyEngine on
        ProxyPass / http://server.city:1000/ nocanon  # 或者端口 11434
        ProxyPassReverse / http://server.city:1000/  # 或者端口 11434
        SSLCertificateFile /etc/letsencrypt/live/models.server.city/fullchain.pem
        SSLCertificateKeyFile /etc/letsencrypt/live/models.server.city/privkey.pem
        Include /etc/letsencrypt/options-ssl-apache.conf
    </VirtualHost>
</IfModule>
```

如果尚未启用站点，请先启用（如果还没有这样做）：

```bash
a2ensite models.server.city.conf
```

#### 对于 Ollama 服务器的 SSL 部分

运行以下命令：

导航到 apache sites-available 目录：

```bash
cd /etc/apache2/sites-available/
certbot --apache -d server.com
```

```bash
<VirtualHost 192.168.254.109:80>
    DocumentRoot "/var/www/html/"
    ServerName models.server.city
    <Directory "/var/www/html/">
        Options None
        Require all granted
    </Directory>
    ProxyRequests Off
    ProxyPreserveHost On
    ProxyAddHeaders On
    SSLProxyEngine on
    ProxyPass / http://server.city:1000/ nocanon  # 或者端口 11434
    ProxyPassReverse / http://server.city:1000/  # 或者端口 11434
    RewriteEngine on
    RewriteCond %{SERVER_NAME} =models.server.city
    RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
</VirtualHost>
```

不要忘记重启或重新加载 Apache：`systemctl reload apache2`

打开你的站点：https://server.com！

**恭喜**，你的 **Open-AI-like Chat-GPT 风格 UI** 现在正在提供具有 RAG、RBAC 和多模态功能的 AI 服务！如果还没有下载 Ollama 模型，请尽快下载！

如果遇到任何配置错误或问题，请提交问题或参与讨论。这里有大量友好的开发者可以帮助你。

让我们共同努力，使这个 UI 更加友好！

感谢您选择 open-webui 作为您的 AI 用户界面！

此文档由 **Bob Reyes** 制作，他是来自菲律宾的 **Open-WebUI** 粉丝。
