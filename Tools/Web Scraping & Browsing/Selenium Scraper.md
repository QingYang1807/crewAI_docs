# Selenium 爬虫工具

> `SeleniumScrapingTool` 旨在使用 Selenium 提取和读取指定网站的内容。

# `SeleniumScrapingTool`

<Note>
  该工具目前正在开发中。随着我们完善其功能，用户可能会遇到意外行为。
  您的反馈对我们进行改进非常宝贵。
</Note>

## 描述

`SeleniumScrapingTool` 专为高效的网页爬虫任务而设计。
它允许通过使用 CSS 选择器精确定位特定元素，从而精确提取网页内容。
其设计满足了广泛的爬虫需求，提供了灵活性以适应任何提供的网站 URL。

## 安装

要使用此工具，您需要安装 CrewAI 工具包和 Selenium：

```shell  theme={null}
pip install 'crewai[tools]'
uv add selenium webdriver-manager
```

您还需要在系统上安装 Chrome，因为该工具使用 Chrome WebDriver 进行浏览器自动化。

## 示例

以下示例演示了如何将 `SeleniumScrapingTool` 与 CrewAI 代理一起使用：

```python Code theme={null}
from crewai import Agent, Task, Crew, Process
from crewai_tools import SeleniumScrapingTool

# 初始化工具
selenium_tool = SeleniumScrapingTool()

# 定义使用该工具的代理
web_scraper_agent = Agent(
    role="网页爬虫",
    goal="使用 Selenium 从网站提取信息",
    backstory="一位专业的网页爬虫专家，能够从动态网站提取内容。",
    tools=[selenium_tool],
    verbose=True,
)

# 从网站爬取内容的示例任务
scrape_task = Task(
    description="从 example.com 主页提取主要内容。使用 CSS 选择器 'main' 来定位主要内容区域。",
    expected_output="来自 example.com 主页的主要内容。",
    agent=web_scraper_agent,
)

# 创建并运行团队
crew = Crew(
    agents=[web_scraper_agent],
    tasks=[scrape_task],
    verbose=True,
    process=Process.sequential,
)
result = crew.kickoff()
```

您也可以使用预定义参数初始化工具：

```python Code theme={null}
# 使用预定义参数初始化工具
selenium_tool = SeleniumScrapingTool(
    website_url='https://example.com',
    css_element='.main-content',
    wait_time=5
)

# 定义使用该工具的代理
web_scraper_agent = Agent(
    role="网页爬虫",
    goal="使用 Selenium 从网站提取信息",
    backstory="一位专业的网页爬虫专家，能够从动态网站提取内容。",
    tools=[selenium_tool],
    verbose=True,
)
```

## 参数

`SeleniumScrapingTool` 在初始化时接受以下参数：

* **website\_url**: 可选。要爬取的网站 URL。如果在初始化时提供，代理在使用该工具时将不需要指定它。
* **css\_element**: 可选。要提取元素的 CSS 选择器。如果在初始化时提供，代理在使用该工具时将不需要指定它。
* **cookie**: 可选。包含 cookie 信息的字典，用于模拟已登录会话以访问受限内容。
* **wait\_time**: 可选。指定爬取前的延迟时间（秒），允许网站和任何动态内容完全加载。默认为 `3` 秒。
* **return\_html**: 可选。是否返回 HTML 内容而不仅仅是文本。默认为 `False`。

当与代理一起使用该工具时，代理将需要提供以下参数（除非它们在初始化时已指定）：

* **website\_url**: 必需。要爬取的网站 URL。
* **css\_element**: 必需。要提取元素的 CSS 选择器。

## 代理集成示例

以下是如何将 `SeleniumScrapingTool` 与 CrewAI 代理集成的更详细示例：

```python Code theme={null}
from crewai import Agent, Task, Crew, Process
from crewai_tools import SeleniumScrapingTool

# 初始化工具
selenium_tool = SeleniumScrapingTool()

# 定义使用该工具的代理
web_scraper_agent = Agent(
    role="网页爬虫",
    goal="从动态网站提取并分析信息",
    backstory="""您是一位专业的网页爬虫专家，专门提取需要浏览器自动化的动态网站内容。
    您拥有丰富的 CSS 选择器知识，能够识别正确的选择器来定位任何网站上的特定内容。""",
    tools=[selenium_tool],
    verbose=True,
)

# 为代理创建任务
scrape_task = Task(
    description="""
    从 {website_url} 的新闻网站提取以下信息：
  
    1. 所有特色文章的标题（CSS 选择器：'.headline'）
    2. 这些文章的发布日期（CSS 选择器：'.pub-date'）
    3. 可用的作者姓名（CSS 选择器：'.author'）
  
    将这些信息编译成结构化格式，将每篇文章的详细信息组合在一起。
    """,
    expected_output="包含文章标题、发布日期和作者的结构化列表。",
    agent=web_scraper_agent,
)

# 运行任务
crew = Crew(
    agents=[web_scraper_agent],
    tasks=[scrape_task],
    verbose=True,
    process=Process.sequential,
)
result = crew.kickoff(inputs={"website_url": "https://news-example.com"})
```

## 实现细节

`SeleniumScrapingTool` 使用 Selenium WebDriver 来自动化浏览器交互：

```python Code theme={null}
class SeleniumScrapingTool(BaseTool):
    name: str = "读取网站内容"
    description: str = "可用于读取网站内容的工具。"
    args_schema: Type[BaseModel] = SeleniumScrapingToolSchema
  
    def _run(self, **kwargs: Any) -> Any:
        website_url = kwargs.get("website_url", self.website_url)
        css_element = kwargs.get("css_element", self.css_element)
        return_html = kwargs.get("return_html", self.return_html)
        driver = self._create_driver(website_url, self.cookie, self.wait_time)

        content = self._get_content(driver, css_element, return_html)
        driver.close()

        return "\n".join(content)
```

该工具执行以下步骤：

1. 创建无头 Chrome 浏览器实例
2. 导航到指定的 URL
3. 等待指定时间以允许页面加载
4. 如果提供了 cookie，则添加它们
5. 根据 CSS 选择器提取内容
6. 将提取的内容作为文本或 HTML 返回
7. 关闭浏览器实例

## 处理动态内容

`SeleniumScrapingTool` 对于爬取通过 JavaScript 加载动态内容的网站特别有用。通过使用真实的浏览器实例，它可以：

1. 在页面上执行 JavaScript
2. 等待动态内容加载
3. 如果需要，可以与元素交互
4. 提取使用简单 HTTP 请求无法获取的内容

您可以调整 `wait_time` 参数，以确保所有动态内容在提取之前都已加载。

## 结论

`SeleniumScrapingTool` 提供了一种使用浏览器自动化从网站提取内容的强大方法。通过使代理能够像真实用户一样与网站交互，它促进了使用更简单方法难以或无法提取的动态内容的爬取。该工具对于涉及 JavaScript 渲染内容的现代 Web 应用程序的研究、数据收集和监控任务特别有用。