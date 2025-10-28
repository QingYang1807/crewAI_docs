# Firecrawl 爬取网站

> `FirecrawlCrawlWebsiteTool` 旨在爬取网站并将其转换为干净的 markdown 或结构化数据。

# `FirecrawlCrawlWebsiteTool`

## 描述

[Firecrawl](https://firecrawl.dev) 是一个用于爬取并将任何网站转换为干净 markdown 或结构化数据的平台。

## 安装

* 从 [firecrawl.dev](https://firecrawl.dev) 获取 API 密钥，并将其设置在环境变量 (`FIRECRAWL_API_KEY`) 中。
* 安装 [Firecrawl SDK](https://github.com/mendableai/firecrawl) 以及 `crewai[tools]` 包：

```shell  theme={null}
pip install firecrawl-py 'crewai[tools]'
```

## 示例

如下使用 FirecrawlScrapeFromWebsiteTool 来允许您的代理加载网站：

```python Code theme={null}
from crewai_tools import FirecrawlCrawlWebsiteTool

tool = FirecrawlCrawlWebsiteTool(url='firecrawl.dev')
```

## 参数

* `api_key`: 可选。指定 Firecrawl API 密钥。默认为 `FIRECRAWL_API_KEY` 环境变量。
* `url`: 开始爬取的基础 URL。
* `page_options`: 可选。
  * `onlyMainContent`: 可选。仅返回页面的主要内容，不包括页眉、导航、页脚等。
  * `includeHtml`: 可选。包含页面的原始 HTML 内容。将在响应中输出一个 html 键。
* `crawler_options`: 可选。用于控制爬取行为的选项。
  * `includes`: 可选。要包含在爬取中的 URL 模式。
  * `exclude`: 可选。要从爬取中排除的 URL 模式。
  * `generateImgAltText`: 可选。使用 LLM 为图片生成替代文本（需要付费计划）。
  * `returnOnlyUrls`: 可选。如果为 true，则在爬取状态中仅返回 URL 列表。注意：响应将是数据中的 URL 列表，而不是文档列表。
  * `maxDepth`: 可选。爬取的最大深度。深度 1 是基础 URL，深度 2 包括基础 URL 及其直接子级，依此类推。
  * `mode`: 可选。要使用的爬取模式。快速模式在没有网站地图的网站上爬取速度快 4 倍，但可能不那么准确，不应在大量 JavaScript 渲染的网站上使用。
  * `limit`: 可选。要爬取的最大页面数。
  * `timeout`: 可选。爬取操作的超时时间（以毫秒为单位）。