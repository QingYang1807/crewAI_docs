# Scrapegraph 抓取工具

> `ScrapegraphScrapeTool` 利用 Scrapegraph AI 的 SmartScraper API 来智能地从网站提取内容。

# `ScrapegraphScrapeTool`

## 描述

`ScrapegraphScrapeTool` 旨在利用 Scrapegraph AI 的 SmartScraper API 来智能地从网站提取内容。该工具提供了基于人工智能内容提取的高级网页抓取功能，使其非常适合有针对性的数据收集和内容分析任务。与传统网页抓取器不同，它能够理解网页的上下文和结构，以根据自然语言提示提取最相关的信息。

## 安装

要使用此工具，您需要安装 Scrapegraph Python 客户端：

```shell  theme={null}
uv add scrapegraph-py
```

您还需要将 Scrapegraph API 密钥设置为环境变量：

```shell  theme={null}
export SCRAPEGRAPH_API_KEY="your_api_key"
```

您可以从 [Scrapegraph AI](https://scrapegraphai.com) 获取 API 密钥。

## 入门步骤

要有效使用 `ScrapegraphScrapeTool`，请按照以下步骤操作：

1. **安装依赖项**：使用上述命令安装所需的软件包。
2. **设置 API 密钥**：将您的 Scrapegraph API 密钥设置为环境变量或在初始化时提供。
3. **初始化工具**：使用必要参数创建工具实例。
4. **定义提取提示**：创建自然语言提示来指导特定内容的提取。

## 示例

以下示例演示如何使用 `ScrapegraphScrapeTool` 从网站提取内容：

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import ScrapegraphScrapeTool

# 初始化工具
scrape_tool = ScrapegraphScrapeTool(api_key="your_api_key")

# 定义一个使用该工具的代理
web_scraper_agent = Agent(
    role="网页抓取器",
    goal="从网站提取特定信息",
    backstory="一位网页抓取专家，能够从网页中提取有针对性的内容。",
    tools=[scrape_tool],
    verbose=True,
)

# 从电子商务网站提取产品信息的示例任务
scrape_task = Task(
    description="从 example.com 的特色产品部分提取产品名称、价格和描述。",
    expected_output="包含产品名称、价格和描述的结构化产品信息列表。",
    agent=web_scraper_agent,
)

# 创建并运行团队
crew = Crew(agents=[web_scraper_agent], tasks=[scrape_task])
result = crew.kickoff()
```

您还可以使用预定义参数初始化工具：

```python Code theme={null}
# 使用预定义参数初始化工具
scrape_tool = ScrapegraphScrapeTool(
    website_url="https://www.example.com",
    user_prompt="提取所有产品价格和描述",
    api_key="your_api_key"
)
```

## 参数

`ScrapegraphScrapeTool` 在初始化时接受以下参数：

* **api\_key**：可选。您的 Scrapegraph API 密钥。如果未提供，它将查找 `SCRAPEGRAPH_API_KEY` 环境变量。
* **website\_url**：可选。要抓取的网站 URL。如果在初始化时提供，代理在使用工具时不需要指定它。
* **user\_prompt**：可选。内容提取的自定义指令。如果在初始化时提供，代理在使用工具时不需要指定它。
* **enable\_logging**：可选。是否为 Scrapegraph 客户端启用日志记录。默认为 `False`。

## 使用方法

当将 `ScrapegraphScrapeTool` 与代理一起使用时，代理需要提供以下参数（除非它们在初始化时已指定）：

* **website\_url**：要抓取的网站 URL。
* **user\_prompt**：可选。内容提取的自定义指令。默认为"提取网页的主要内容"。

该工具将根据提供的提示返回提取的内容。

```python Code theme={null}
# 与代理一起使用工具的示例
web_scraper_agent = Agent(
    role="网页抓取器",
    goal="从网站提取特定信息",
    backstory="一位网页抓取专家，能够从网页中提取有针对性的内容。",
    tools=[scrape_tool],
    verbose=True,
)

# 为代理创建一个提取特定内容的任务
extract_task = Task(
    description="从 example.com 提取主要标题和摘要",
    expected_output="网站的主要标题和摘要",
    agent=web_scraper_agent,
)

# 运行任务
crew = Crew(agents=[web_scraper_agent], tasks=[extract_task])
result = crew.kickoff()
```

## 错误处理

`ScrapegraphScrapeTool` 可能会引发以下异常：

* **ValueError**：当 API 密钥缺失或 URL 格式无效时。
* **RateLimitError**：当超过 API 速率限制时。
* **RuntimeError**：当抓取操作失败时（网络问题、API 错误）。

建议指导代理优雅地处理潜在错误：

```python Code theme={null}
# 创建包含错误处理说明的任务
robust_extract_task = Task(
    description="""
    从 example.com 提取主要标题。
    请注意，您可能会遇到以下错误：
    - URL 格式无效
    - 缺少 API 密钥
    - 超过速率限制
    - 网络或 API 错误
  
    如果遇到任何错误，请提供清晰的问题说明
    并建议可能的解决方案。
    """,
    expected_output="提取的标题或清晰的错误说明",
    agent=web_scraper_agent,
)
```

## 速率限制

Scrapegraph API 具有基于您的订阅计划而变化的速率限制。请考虑以下最佳实践：

* 在处理多个 URL 时，在请求之间实现适当的延迟。
* 在应用程序中优雅地处理速率限制错误。
* 在 Scrapegraph 仪表板上检查您的 API 计划限制。

## 实现细节

`ScrapegraphScrapeTool` 使用 Scrapegraph Python 客户端与 SmartScraper API 交互：

```python Code theme={null}
class ScrapegraphScrapeTool(BaseTool):
    """
    一个使用 Scrapegraph AI 智能抓取网站内容的工具。
    """
  
    # 实现细节...
  
    def _run(self, **kwargs: Any) -> Any:
        website_url = kwargs.get("website_url", self.website_url)
        user_prompt = (
            kwargs.get("user_prompt", self.user_prompt)
            or "提取网页的主要内容"
        )

        if not website_url:
            raise ValueError("website_url 是必需的")

        # 验证 URL 格式
        self._validate_url(website_url)

        try:
            # 发出 SmartScraper 请求
            response = self._client.smartscraper(
                website_url=website_url,
                user_prompt=user_prompt,
            )

            return response
        # 错误处理...
```

## 结论

`ScrapegraphScrapeTool` 提供了一种使用人工智能理解网页结构来从网站提取内容的强大方法。通过使代理能够使用自然语言提示定位特定信息，它使网页抓取任务更加高效和专注。该工具特别适用于需要从网页提取特定信息的数据提取、内容监控和研究任务。