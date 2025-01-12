# 翻译步骤

## 大致流程

- 参考 https://docusaurus.nodejs.cn/docs/i18n/introduction

## 1. 配置：在 `docusaurus.config.js` 中声明默认语言环境和替代语言环境

```js
	i18n: {
		defaultLocale: "zh-CN",
		locales: ["en", "zh-CN"],
		localeConfigs: {
			en: {
				label: "English",
			},
			"zh-CN": {
				label: "简体中文",
			},
		},
	}
```

## 2. 翻译：将翻译文件放在正确的文件系统位置

### 部署项目

```js
执行
yarn
yarn run start --locale zh-CN
```

### 1. 翻译React 代码和插件数据

```js
执行
yarn write-translations --locale zh-CN
翻译
i18n\zh-CN\code.json
```

JSON 用于翻译：

- React 代码：`src/pages` 中的独立 React 页面或其他组件
- 通过 `themeConfig` 提供的布局标签：导航栏、页脚
- 通过插件选项提供的布局标签：文档侧边栏类别标签、博客侧边栏标题...

###  2. Markdown 文件

1. 创建目录

```js
mkdir -p i18n/zh-CN/docusaurus-plugin-content-docs/current
mkdir -p i18n/zh-CN/docusaurus-plugin-content-blog
```

2. 复制文件

```js
cp -r docs/** i18n/zh-CN/docusaurus-plugin-content-docs/current
cp -r blog/** i18n/zh-CN/docusaurus-plugin-content-blog
```

3. 翻译json文件的内容

```js
i18n\zh-CN\docusaurus-plugin-content-docs\current.json
i18n\zh-CN\code.json
```

4. 翻译md, mdx文件

```js
node .\i18n\translate-docs.js

i18n\zh-CN\docusaurus-plugin-content-docs\current\getting-started\advanced-topics\env-configuration.md , 文件比较大, 优先手动翻译
```

5. 使用显式标题 ID(可选)

  ```js
yarn write-heading-ids i18n/zh-CN/docusaurus-plugin-content-docs/current
  ```

6. 增加搜索功能
- 参考 https://docusaurus.nodejs.cn/docs/search#algolia-no-search-results
- 参考 https://docusaurus.nodejs.cn/community/resources#search
- 参考 https://jdocs.wiki/docusaurus-site/site-creation-guide/docusaurus-lunr-search
```sh
添加依赖
yarn add docusaurus-lunr-search
yarn add @node-rs/jieba
构建
yarn add docusaurus-lunr-search
运行
serve build
```
# Website

This website is built using [Docusaurus](https://docusaurus.io/), a modern static website generator.

## Installation

```sh
npm ci
```

## Local Development

```sh
npm start
```

This command starts a local development server and opens up a browser window. Most changes are reflected live without having to restart the server.

## Build

```sh
npm run build
```

This command generates static content into the `build` directory and can be served using any static contents hosting service.

## Deployment

Using SSH:

```sh
USE_SSH=true npm run deploy
```

Not using SSH:

```sh
GIT_USER=<Your GitHub username> npm run deploy
```

If you are using GitHub pages for hosting, this command is a convenient way to build the website and push to the `gh-pages` branch.
