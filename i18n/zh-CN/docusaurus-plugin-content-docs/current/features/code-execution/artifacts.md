---
sidebar_position: 1
title: "🏺 Artifacts"
---


# 什么是 Artifacts，如何在 Open WebUI 中使用它们？

Open WebUI 中的 Artifacts 是一项受 Claude.AI 的 Artifacts 启发的创新功能，允许您在聊天中与由大型语言模型生成的重要且独立的内容进行交互。您可以单独查看、修改、构建或引用这些内容，而不必依赖于主要对话，从而更轻松地处理复杂输出，并确保日后能够重新访问重要信息。

## Open WebUI 何时使用 Artifacts？

当生成的内容符合我们平台特定的标准时，Open WebUI 就会创建一个 Artifact：

1. **可渲染性**：要作为 Artifact 显示，内容必须是 Open WebUI 支持的格式。这包括：

* 单页 HTML 网站
* 可缩放矢量图形（SVG）图像
* 完整网页，包含 HTML、JavaScript 和 CSS 都在一个 Artifact 中。请注意，如果生成完整网页，HTML 是必需的。
* ThreeJS 可视化和其他 JavaScript 可视化库，如 D3.js。

其他内容类型，如文档（Markdown 或纯文本）、代码片段和 React 组件，不会被 Open WebUI 渲染为 Artifacts。

## Open WebUI 的模型如何创建 Artifacts？

要在 Open WebUI 中使用 Artifacts，模型必须提供触发渲染过程以创建 Artifact 的内容。这涉及生成有效的 HTML、SVG 代码或其他支持的格式用于 Artifact 渲染。当生成的内容符合上述标准时，Open WebUI 会将其显示为交互式 Artifact。

## 如何在 Open WebUI 中使用 Artifacts？

当 Open WebUI 创建一个 Artifact 时，您会在主聊天窗口右侧看到一个专门的 Artifact 窗口。以下是如何与 Artifacts 交互的方法：

* **编辑和迭代**：在聊天中询问 LLM 修改或迭代内容，这些更新将直接显示在 Artifact 窗口中。您可以通过位于 Artifact 左下角的版本选择器在不同版本之间切换。每次编辑都会创建一个新版本，使您能够通过版本选择器跟踪更改。
* **更新**：根据您的消息，Open WebUI 可能会更新现有 Artifact。Artifact 窗口将显示最新内容。
* **操作**：访问 Artifact 的附加操作，例如复制内容或全屏打开 Artifact，这些操作位于 Artifact 的右下角。

## 编辑 Artifacts

1. **针对性更新**：描述您希望更改的内容及其位置。例如：

* “将图表中的柱状图颜色从蓝色改为红色。”
* “将 SVG 图像的标题更新为‘新标题’。”

2. **全面重写**：请求对大部分内容进行重大更改，涉及结构重组或多个部分的更新。例如：

* “将这个单页 HTML 网站改造成一个落地页。”
* “重新设计这个 SVG 图形，使其使用 ThreeJS 进行动画效果。”

**最佳实践**：

* 明确指出您希望更改 Artifact 的哪一部分。
* 在进行针对性更新时，引用周围的唯一标识文本。
* 考虑小更新还是全面重写更适合您的需求。

## 使用场景

Open WebUI 中的 Artifacts 使各种团队能够快速高效地创建高质量的作品。以下是一些针对我们平台的示例：

* **设计师**：
  * 创建交互式的 SVG 图形用于 UI/UX 设计。
  * 设计单页 HTML 网站或落地页。
* **开发者**：创建简单的 HTML 原型或为项目生成 SVG 图标。
* **市场营销人员**：
  * 设计带有性能指标的营销活动落地页。
  * 创建用于广告创意或社交媒体帖子的 SVG 图形。

## 您可以使用 Open WebUI 的 Artifacts 创建的项目示例

Open WebUI 中的 Artifacts 使各种团队和个人能够快速高效地创建高质量的作品。以下是一些针对我们平台的示例，展示了 Artifacts 的多功能性并激发您探索其潜力：

1. **交互式可视化**

* 使用组件：ThreeJS、D3.js 和 SVG
* 优势：通过交互式可视化创建引人入胜的数据故事。Open WebUI 的 Artifacts 使您能够在不同版本之间切换，更容易测试不同的可视化方法并跟踪更改。

示例项目：使用 ThreeJS 构建一个交互式折线图，以可视化股票价格随时间的变化。在不同版本中分别更新图表的颜色和比例，以比较不同的可视化策略。

2. **单页 Web 应用程序**

* 使用组件：HTML、CSS 和 JavaScript
* 优势：直接在 Open WebUI 中开发单页 Web 应用程序。使用针对性更新和全面重写编辑和迭代内容。

示例项目：使用 HTML 和 CSS 设计一个待办事项应用程序的用户界面。使用 JavaScript 添加交互功能。通过针对性更新和全面重写更新应用程序的布局和 UI/UX。

3. **动画 SVG 图形**

* 使用组件：SVG 和 ThreeJS
* 优势：为营销活动、社交媒体或网页设计创建引人注目的动画 SVG 图形。Open WebUI 的 Artifacts 使您能够在单一窗口中编辑和迭代图形。

示例项目：为公司品牌设计一个动画 SVG 标志。使用 ThreeJS 添加动画效果，并通过 Open WebUI 的针对性更新来优化标志的颜色和设计。

4. **网页原型**

* 使用组件：HTML、CSS 和 JavaScript
* 优势：直接在 Open WebUI 中构建和测试网页原型。通过版本选择器在不同设计方法之间切换，以细化原型。

示例项目：使用 HTML、CSS 和 JavaScript 开发一个新的电子商务网站的原型。使用 Open WebUI 的针对性更新来优化导航、布局和 UI/UX。

5. **交互式叙事**

* 使用组件：HTML、CSS 和 JavaScript
* 优势：创建具有滚动效果、动画和其他交互元素的交互式故事。Open WebUI 的 Artifacts 使您能够完善故事并通过测试不同版本来优化它。

示例项目：构建一个关于公司历史的互动故事，使用滚动效果和动画吸引读者。通过针对性更新来完善故事的叙述，并使用 Open WebUI 的版本选择器来测试不同版本。