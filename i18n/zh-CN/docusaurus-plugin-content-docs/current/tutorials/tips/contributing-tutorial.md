---
sidebar_position: 2
title: "🤝 贡献教程"
---

:::warning
本教程由社区贡献，并未得到 OpenWebUI 团队的支持。它仅作为如何根据您的具体需求定制 OpenWebUI 的示例。想要贡献？请查看贡献教程。
:::

# 贡献教程

感谢您对 Open WebUI 文档的教程贡献感兴趣。请按照以下步骤设置环境并提交您的教程。

## 步骤

1. **分叉 `openwebui/docs` GitHub 仓库**

   - 访问 GitHub 上的 [Open WebUI Docs 仓库](https://github.com/open-webui/docs)。
   - 点击右上角的 **Fork** 按钮，将仓库复制到您的 GitHub 账户下。

2. **启用 GitHub Actions**

   - 在您分叉的仓库中，导航至 **Actions** 标签页。
   - 如果有提示，请按照屏幕上的指示启用 GitHub Actions。

3. **启用 GitHub Pages**

   - 进入分叉后的仓库中的 **Settings** > **Pages**。
   - 在 **Source** 下选择要部署的分支（例如 `main`）和文件夹（例如 `/docs`）。
   - 点击 **Save** 启用 GitHub Pages。

4. **配置 GitHub 环境变量**

   - 在您分叉的仓库中，进入 **Settings** > **Secrets and variables** > **Actions** > **Variables**。
   - 添加以下环境变量：
     - `BASE_URL` 设置为 `/docs`（或您选择的基础 URL）。
     - `SITE_URL` 设置为 `https://<your-github-username>.github.io/`。

### 📝 更新 GitHub Pages 工作流和配置文件

如果您需要调整部署设置以适应自定义设置，可以按以下步骤操作：

a. **更新 `.github/workflows/gh-pages.yml`**

- 如果需要，在构建步骤中添加 `BASE_URL` 和 `SITE_URL` 环境变量：

     ```yaml
       - name: Build
         env:
           BASE_URL: ${{ vars.BASE_URL }}
           SITE_URL: ${{ vars.SITE_URL }}
         run: npm run build
     ```

b. **修改 `docusaurus.config.ts` 以使用环境变量**

- 更新 `docusaurus.config.ts` 以使用这些环境变量，并为本地或直接部署提供默认值：

     ```typescript
     const config: Config = {
       title: "Open WebUI",
       tagline: "LLM 的 ChatGPT 风格 WebUI（原 Ollama WebUI）",
       favicon: "img/favicon.png",
       url: process.env.SITE_URL || "https://openwebui.com",
       baseUrl: process.env.BASE_URL || "/",
       ...
     };
     ```

- 这种设置确保了分叉和自定义设置的一致性部署行为。

5. **运行 `gh-pages` GitHub 工作流**

   - 在 **Actions** 标签页中，找到 `gh-pages` 工作流。
   - 根据需要手动触发工作流，或者根据您的设置自动运行。

6. **浏览您的分叉副本**

   - 访问 `https://<your-github-username>.github.io/<BASE_URL>` 查看您的分叉文档。

7. **撰写您的更改**

   - 在您分叉的仓库中，导航到适当的目录（例如 `docs/tutorial/`）。
   - 创建一个新的 Markdown 文件用于您的教程，或编辑现有的文件。
   - 确保您的教程包含不支持的警告横幅。

8. **提交拉取请求**

   - 当您的教程准备就绪后，将更改提交到您分叉的仓库。
   - 导航到原始的 `open-webui/docs` 仓库。
   - 点击 **New Pull Request** 并选择您的分叉和分支作为源。
   - 提供一个描述性的标题和说明。
   - 提交拉取请求以供审核。

## 注意事项

社区贡献的教程必须包含以下内容：

```
:::warning
本教程由社区贡献，并未得到 OpenWebUI 团队的支持。它仅作为如何根据您的具体需求定制 OpenWebUI 的示例。想要贡献？请查看贡献教程。
:::
```

---

:::tip 如何在本地测试 Docusaurus  
您可以使用以下命令在本地测试您的 Docusaurus 站点：

```bash
npm install   # 安装依赖
npm run build # 构建生产版本站点
```

这将帮助您在部署前发现任何问题。
:::

---
