---
title: "🔒 使用 Nginx 实现 HTTPS"
---

:::warning
此教程由社区贡献，未得到 OpenWebUI 团队的支持。它仅作为如何根据特定需求定制 OpenWebUI 的示例。想要贡献？请参阅贡献指南。
:::

# 使用 Nginx 实现 HTTPS

确保用户与 Open WebUI 之间的通信安全至关重要。HTTPS（超文本传输安全协议）加密传输的数据，防止窃听和篡改。通过将 Nginx 配置为反向代理，您可以无缝地为 Open WebUI 部署添加 HTTPS，从而增强安全性和可信度。

本指南提供了两种设置 HTTPS 的方法：

- **自签名证书**：适用于开发和内部使用。
- **Let's Encrypt**：适用于需要受信任 SSL 证书的生产环境。

请选择最适合您部署需求的方法。

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

import SelfSigned from './tab-nginx/SelfSigned.md';
import LetsEncrypt from './tab-nginx/LetsEncrypt.md';

<Tabs>
  <TabItem value="self-signed" label="自签名证书">
    <SelfSigned />
  </TabItem>

  <TabItem value="letsencrypt" label="Let's Encrypt">
    <LetsEncrypt />
  </TabItem>
</Tabs>

## 下一步

设置好 HTTPS 后，您可以通过以下地址安全访问 Open WebUI：

- [https://localhost](https://localhost)

如果您使用域名，请确保 DNS 记录配置正确。对于生产环境，建议使用 Let's Encrypt 获取受信任的 SSL 证书。

---
