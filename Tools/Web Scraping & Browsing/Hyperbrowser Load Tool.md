# Hyperbrowser 加载工具

> `HyperbrowserLoadTool` 能够使用 Hyperbrowser 进行网页抓取和爬取。

# `HyperbrowserLoadTool`

## 描述

`HyperbrowserLoadTool` 能够使用 [Hyperbrowser](https://hyperbrowser.ai) 进行网页抓取和爬取，Hyperbrowser 是一个用于运行和扩展无头浏览器的平台。此工具允许您抓取单个页面或爬取整个网站，并以正确格式的 markdown 或 HTML 返回内容。

主要特点：

* 即时扩展性 - 在几秒钟内启动数百个浏览器会话，无需担心基础设施问题
* 简单集成 - 与 Puppeteer 和 Playwright 等流行工具无缝协作
* 强大的 API - 提供易于使用的 API 来抓取/爬取任何网站
* 绕过反机器人措施 - 内置隐身模式、广告拦截、自动 CAPTCHA 解决和轮换代理

## 安装

要使用此工具，您需要安装 Hyperbrowser SDK：

```shell  theme={null}
uv add hyperbrowser
```

## 入门步骤

要有效使用 `HyperbrowserLoadTool`，请按照以下步骤操作：

1. **注册**：前往 [Hyperbrowser](https://app.hyperbrowser.ai/) 注册并生成 API 密钥。
2. **API 密钥**：设置 `HYPERBROWSER_API_KEY` 环境变量或直接将其传递给工具构造函数。
3. **安装 SDK**：使用上述命令安装 Hyperbrowser SDK。

## 示例

以下示例演示了如何初始化工具并使用它来抓取网站：

```python Code theme={null}
from crewai_tools import HyperbrowserLoadTool
from crewai import Agent

# 使用您的 API 密钥初始化工具
tool = HyperbrowserLoadTool(api_key="your_api_key")  # 或使用环境变量

# 定义一个使用该工具的代理
@agent
def web_researcher(self) -> Agent:
    '''
    该代理使用 HyperbrowserLoadTool 抓取网站
    并提取信息。
    '''
    return Agent(
        config=self.agents_config["web_researcher"],
        tools=[tool]
    )
```

## 参数

`HyperbrowserLoadTool` 接受以下参数：

### 构造函数参数

* **api\_key**：可选。您的 Hyperbrowser API 密钥。如果未提供，将从 `HYPERBROWSER_API_KEY` 环境变量中读取。

### 运行参数

* **url**：必需。要抓取或爬取的网站 URL。
* **operation**：可选。要对网站执行的操作。可以是 'scrape'（抓取）或 'crawl'（爬取）。默认为 'scrape'。
* **params**：可选。抓取或爬取操作的附加参数。

## 支持的参数

有关所有支持参数的详细信息，请访问：

* [抓取参数](https://docs.hyperbrowser.ai/reference/sdks/python/scrape#start-scrape-job-and-wait)
* [爬取参数](https://docs.hyperbrowser.ai/reference/sdks/python/crawl#start-crawl-job-and-wait)

## 返回格式

该工具以以下格式返回内容：

* 对于 **scrape** 操作：页面内容以 markdown 或 HTML 格式返回。
* 对于 **crawl** 操作：每个页面的内容由分隔符分隔，包括每个页面的 URL。

## 结论

`HyperbrowserLoadTool` 提供了一种强大的方式来抓取和爬取网站，能够处理反机器人措施、CAPTCHA 等复杂场景。通过利用 Hyperbrowser 的平台，该工具使代理能够高效地访问和提取网页内容。