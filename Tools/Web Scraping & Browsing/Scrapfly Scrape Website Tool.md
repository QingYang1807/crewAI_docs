# Scrapfly 网站抓取工具

> `ScrapflyScrapeWebsiteTool` 利用 Scrapfly 的网页抓取 API 从网站提取各种格式的内容。

# `ScrapflyScrapeWebsiteTool`

## 描述

`ScrapflyScrapeWebsiteTool` 旨在利用 [Scrapfly](https://scrapfly.io/) 的网页抓取 API 从网站提取内容。此工具提供了高级网页抓取功能，包括无头浏览器支持、代理和反机器人绕过功能。它允许以各种格式提取网页数据，包括原始 HTML、markdown 和纯文本，非常适合各种网页抓取任务。

## 安装

要使用此工具，您需要安装 Scrapfly SDK：

```shell theme={null}
uv add scrapfly-sdk
```

您还需要在 [scrapfly.io/register](https://www.scrapfly.io/register/) 注册以获取 Scrapfly API 密钥。

## 入门步骤

要有效使用 `ScrapflyScrapeWebsiteTool`，请按照以下步骤操作：

1. **安装依赖项**：使用上述命令安装 Scrapfly SDK。
2. **获取 API 密钥**：在 Scrapfly 注册以获取您的 API 密钥。
3. **初始化工具**：使用您的 API 密钥创建工具实例。
4. **配置抓取参数**：根据您的需求自定义抓取参数。

## 示例

以下示例演示了如何使用 `ScrapflyScrapeWebsiteTool` 从网站提取内容：

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import ScrapflyScrapeWebsiteTool

# 初始化工具
scrape_tool = ScrapflyScrapeWebsiteTool(api_key="your_scrapfly_api_key")

# 定义使用该工具的代理
web_scraper_agent = Agent(
    role="网页抓取器",
    goal="从网站提取信息",
    backstory="一位能够从任何网站提取内容的网页抓取专家。",
    tools=[scrape_tool],
    verbose=True,
)

# 从网站提取内容的示例任务
scrape_task = Task(
    description="从 https://web-scraping.dev/products 的产品页面提取主要内容并总结可用产品。",
    expected_output="网站上可用产品的摘要。",
    agent=web_scraper_agent,
)

# 创建并运行团队
crew = Crew(agents=[web_scraper_agent], tasks=[scrape_task])
result = crew.kickoff()
```

您也可以自定义抓取参数：

```python Code theme={null}
# 带有自定义抓取参数的示例
web_scraper_agent = Agent(
    role="网页抓取器",
    goal="使用自定义参数从网站提取信息",
    backstory="一位能够从任何网站提取内容的网页抓取专家。",
    tools=[scrape_tool],
    verbose=True,
)

# 代理将使用以下参数的工具：
# url="https://web-scraping.dev/products"
# scrape_format="markdown"
# ignore_scrape_failures=True
# scrape_config={
#     "asp": True,  # 绕过抓取阻止解决方案，如 Cloudflare
#     "render_js": True,  # 使用云无头浏览器启用 JavaScript 渲染
#     "proxy_pool": "public_residential_pool",  # 选择代理池
#     "country": "us",  # 选择代理位置
#     "auto_scroll": True,  # 自动滚动页面
# }

scrape_task = Task(
    description="使用包括 JavaScript 渲染和代理设置在内的高级抓取选项从 https://web-scraping.dev/products 的产品页面提取主要内容。",
    expected_output="包含所有可用信息的产品的详细摘要。",
    agent=web_scraper_agent,
)
```

## 参数

`ScrapflyScrapeWebsiteTool` 接受以下参数：

### 初始化参数

* **api_key**：必需。您的 Scrapfly API 密钥。

### 运行参数

* **url**：必需。要抓取的网站 URL。
* **scrape_format**：可选。提取网页内容的格式。选项包括 "raw"（HTML）、"markdown" 或 "text"。默认为 "markdown"。
* **scrape_config**：可选。包含额外 Scrapfly 抓取配置选项的字典。
* **ignore_scrape_failures**：可选。是否忽略抓取过程中的失败。如果设置为 `True`，当抓取失败时，工具将返回 `None` 而不是引发异常。

## Scrapfly 配置选项

`scrape_config` 参数允许您使用以下选项自定义抓取行为：

* **asp**：启用反抓取保护绕过。
* **render_js**：使用云无头浏览器启用 JavaScript 渲染。
* **proxy_pool**：选择代理池（例如，"public_residential_pool"、"datacenter"）。
* **country**：选择代理位置（例如，"us"、"uk"）。
* **auto_scroll**：自动滚动页面以加载延迟加载的内容。
* **js**：由无头浏览器执行自定义 JavaScript 代码。

有关配置选项的完整列表，请参阅 [Scrapfly API 文档](https://scrapfly.io/docs/scrape-api/getting-started)。

## 用法

与代理一起使用 `ScrapflyScrapeWebsiteTool` 时，代理需要提供要抓取的网站 URL，并可以选择指定格式和额外配置选项：

```python Code theme={null}
# 与代理一起使用工具的示例
web_scraper_agent = Agent(
    role="网页抓取器",
    goal="从网站提取信息",
    backstory="一位能够从任何网站提取内容的网页抓取专家。",
    tools=[scrape_tool],
    verbose=True,
)

# 为代理创建任务
scrape_task = Task(
    description="以 markdown 格式从 example.com 提取主要内容。",
    expected_output="以 markdown 格式呈现的 example.com 主要内容。",
    agent=web_scraper_agent,
)

# 运行任务
crew = Crew(agents=[web_scraper_agent], tasks=[scrape_task])
result = crew.kickoff()
```

对于使用自定义配置的高级用法：

```python Code theme={null}
# 创建具有更具体说明的任务
advanced_scrape_task = Task(
    description="""
    按照以下要求从 example.com 提取内容：
    - 将内容转换为纯文本格式
    - 启用 JavaScript 渲染
    - 使用基于美国的代理
    - 优雅地处理任何抓取失败
    """,
    expected_output="从 example.com 提取的内容",
    agent=web_scraper_agent,
)
```

## 错误处理

默认情况下，`ScrapflyScrapeWebsiteTool` 在抓取失败时会引发异常。可以通过指定 `ignore_scrape_failures` 参数指示代理优雅地处理失败：

```python Code theme={null}
# 创建指示代理处理错误的任务
error_handling_task = Task(
    description="""
    从可能存在问题的网站提取内容，并确保通过将 ignore_scrape_failures 设置为 True 来优雅地处理任何抓取失败。
    """,
    expected_output="提取的内容或优雅的错误消息",
    agent=web_scraper_agent,
)
```

## 实现细节

`ScrapflyScrapeWebsiteTool` 使用 Scrapfly SDK 与 Scrapfly API 交互：

```python Code theme={null}
class ScrapflyScrapeWebsiteTool(BaseTool):
    name: str = "Scrapfly 网页抓取 API 工具"
    description: str = (
        "使用 Scrapfly 抓取网页 url 并以 markdown 或文本格式返回其内容"
    )
  
    # 实现细节...
  
    def _run(
        self,
        url: str,
        scrape_format: str = "markdown",
        scrape_config: Optional[Dict[str, Any]] = None,
        ignore_scrape_failures: Optional[bool] = None,
    ):
        from scrapfly import ScrapeApiResponse, ScrapeConfig

        scrape_config = scrape_config if scrape_config is not None else {}
        try:
            response: ScrapeApiResponse = self.scrapfly.scrape(
                ScrapeConfig(url, format=scrape_format, **scrape_config)
            )
            return response.scrape_result["content"]
        except Exception as e:
            if ignore_scrape_failures:
                logger.error(f"从 {url} 获取数据时出错，异常：{e}")
                return None
            else:
                raise e
```

## 结论

`ScrapflyScrapeWebsiteTool` 提供了一种使用 Scrapfly 高级网页抓取功能从网站提取内容的强大方式。凭借无头浏览器支持、代理和反机器人绕过等功能，它可以处理复杂的网站并以各种格式提取内容。此工具特别适用于需要可靠网页抓取的数据提取、内容监控和研究任务。