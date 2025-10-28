# Firecrawl 网站抓取工具

> `FirecrawlScrapeWebsiteTool` 旨在抓取网站并将其转换为干净的 Markdown 或结构化数据。

# `FirecrawlScrapeWebsiteTool`

## 描述

[Firecrawl](https://firecrawl.dev) 是一个用于抓取并将任何网站转换为干净 Markdown 或结构化数据的平台。

## 安装

* 从 [firecrawl.dev](https://firecrawl.dev) 获取 API 密钥，并将其设置在环境变量（`FIRECRAWL_API_KEY`）中。
* 安装 [Firecrawl SDK](https://github.com/mendableai/firecrawl) 以及 `crewai[tools]` 包：

```shell  theme={null}
pip install firecrawl-py 'crewai[tools]'
```

## 示例

如下使用 FirecrawlScrapeWebsiteTool，让您的代理能够加载网站：

```python Code theme={null}
from crewai_tools import FirecrawlScrapeWebsiteTool

tool = FirecrawlScrapeWebsiteTool(url='firecrawl.dev')
```

## 参数

* `api_key`: 可选。指定 Firecrawl API 密钥。默认为 `FIRECRAWL_API_KEY` 环境变量。
* `url`: 要抓取的 URL。
* `page_options`: 可选。
  * `onlyMainContent`: 可选。仅返回页面的主要内容，不包括标题、导航、页脚等。
  * `includeHtml`: 可选。包含页面的原始 HTML 内容。将在响应中输出一个 html 键。
* `extractor_options`: 可选。用于从页面内容中进行基于 LLM 的结构化信息提取的选项。
  * `mode`: 使用的提取模式，目前支持 'llm-extraction'
  * `extractionPrompt`: 可选。描述要从页面中提取什么信息的提示
  * `extractionSchema`: 可选。要提取的数据的模式
* `timeout`: 可选。请求的超时时间（毫秒）