# 工具

> 在 CrewAI 框架内理解和利用工具，以实现智能体协作和任务执行。

## 概述

CrewAI 工具为智能体提供了从网络搜索和数据分析到协作和同事间任务委托等各种能力。
本文档概述了如何在 CrewAI 框架内创建、集成和利用这些工具，包括新增的协作工具重点介绍。

## 什么是工具？

CrewAI 中的工具是智能体可以用来执行各种操作的技能或功能。
这包括来自 [CrewAI 工具包](https://github.com/joaomdmoura/crewai-tools) 和 [LangChain 工具](https://python.langchain.com/docs/integrations/tools) 的工具，
支持从简单搜索到复杂交互以及智能体间有效团队合作的所有功能。

<Note type="info" title="企业增强版：工具库">
  CrewAI AMP 提供了一个全面的工具库，包含常见业务系统和 API 的预构建集成。在几分钟而不是几天内部署配备企业工具的智能体。

  企业工具库包括：

  * 流行的企业系统预构建连接器
  * 自定义工具创建界面
  * 版本控制和共享功能
  * 安全和合规特性
</Note>

## 工具的关键特性

* **实用性**：为网络搜索、数据分析、内容生成和智能体协作等任务而设计。
* **集成性**：通过将工具无缝集成到工作流程中，增强智能体能力。
* **可定制性**：提供了开发自定义工具或利用现有工具的灵活性，以满足智能体的特定需求。
* **错误处理**：结合强大的错误处理机制，确保顺畅运行。
* **缓存机制**：具有智能缓存功能，优化性能并减少冗余操作。
* **异步支持**：处理同步和异步工具，实现非阻塞操作。

## 使用 CrewAI 工具

要使用 crewAI 工具增强您的智能体能力，首先安装我们的额外工具包：

```bash  theme={null}
pip install 'crewai[tools]'
```

以下是使用示例：

```python Code theme={null}
import os
from crewai import Agent, Task, Crew
# 导入 crewAI 工具
from crewai_tools import (
    DirectoryReadTool,
    FileReadTool,
    SerperDevTool,
    WebsiteSearchTool
)

# 设置 API 密钥
os.environ["SERPER_API_KEY"] = "Your Key" # serper.dev API 密钥
os.environ["OPENAI_API_KEY"] = "Your Key"

# 实例化工具
docs_tool = DirectoryReadTool(directory='./blog-posts')
file_tool = FileReadTool()
search_tool = SerperDevTool()
web_rag_tool = WebsiteSearchTool()

# 创建智能体
researcher = Agent(
    role='Market Research Analyst',
    goal='Provide up-to-date market analysis of the AI industry',
    backstory='An expert analyst with a keen eye for market trends.',
    tools=[search_tool, web_rag_tool],
    verbose=True
)

writer = Agent(
    role='Content Writer',
    goal='Craft engaging blog posts about the AI industry',
    backstory='A skilled writer with a passion for technology.',
    tools=[docs_tool, file_tool],
    verbose=True
)

# 定义任务
research = Task(
    description='Research the latest trends in the AI industry and provide a summary.',
    expected_output='A summary of the top 3 trending developments in the AI industry with a unique perspective on their significance.',
    agent=researcher
)

write = Task(
    description='Write an engaging blog post about the AI industry, based on the research analyst's summary. Draw inspiration from the latest blog posts in the directory.',
    expected_output='A 4-paragraph blog post formatted in markdown with engaging, informative, and accessible content, avoiding complex jargon.',
    agent=writer,
    output_file='blog-posts/new_post.md'  # 最终博客文章将保存在这里
)

# 组建一个启用了规划功能的团队
crew = Crew(
    agents=[researcher, writer],
    tasks=[research, write],
    verbose=True,
    planning=True,  # 启用规划功能
)

# 执行任务
crew.kickoff()
```

## 可用的 CrewAI 工具

* **错误处理**：所有工具都内置错误处理功能，使智能体能够优雅地管理异常并继续执行任务。
* **缓存机制**：所有工具都支持缓存，使智能体能够高效地重用先前获得的结果，减少外部资源负载并加快执行时间。您还可以使用工具上的 `cache_function` 属性定义对缓存机制的更精细控制。

以下是可用工具及其描述的列表：

| 工具                             | 描述                                                                                    |
| :------------------------------- | :--------------------------------------------------------------------------------------------- |
| **ApifyActorsTool**              | 将 Apify Actors 集成到您的工作流程中进行网页抓取和自动化任务的工具。 |
| **BrowserbaseLoadTool**          | 用于与网络浏览器交互和提取数据的工具。                             |
| **CodeDocsSearchTool**           | 优化的 RAG 工具，用于搜索代码文档和相关技术文档。 |
| **CodeInterpreterTool**          | 用于解释 Python 代码的工具。                                                           |
| **ComposioTool**                 | 启用 Composio 工具的使用。                                                                 |
| **CSVSearchTool**                | 设计用于在 CSV 文件中搜索的 RAG 工具，专为处理结构化数据而定制。        |
| **DALL-E Tool**                  | 使用 DALL-E API 生成图像的工具。                                             |
| **DirectorySearchTool**          | 用于在目录中搜索的 RAG 工具，对导航文件系统有用。       |
| **DOCXSearchTool**               | 旨在 DOCX 文档中搜索的 RAG 工具，适合处理 Word 文件。          |
| **DirectoryReadTool**            | 便于读取和处理目录结构及其内容。                 |
| **EXASearchTool**                | 设计用于在各种数据源中执行全面搜索的工具。                |
| **FileReadTool**                 | 支持从文件中读取和提取数据，支持多种文件格式。               |
| **FirecrawlSearchTool**          | 使用 Firecrawl 搜索网页并返回结果的工具。                              |
| **FirecrawlCrawlWebsiteTool**    | 使用 Firecrawl 抓取网页的工具。                                                  |
| **FirecrawlScrapeWebsiteTool**   | 使用 Firecrawl 抓取网页 URL 并返回其内容的工具。                   |
| **GithubSearchTool**             | 用于在 GitHub 代码库中搜索的 RAG 工具，对代码和文档搜索有用。             |
| **SerperDevTool**                | 专门用于开发目的的工具，具有正在开发中的特定功能。  |
| **TXTSearchTool**                | 专注于在文本 (.txt) 文件中搜索的 RAG 工具，适合非结构化数据。      |
| **JSONSearchTool**               | 设计用于在 JSON 文件中搜索的 RAG 工具，专为处理结构化数据而设计。     |
| **LlamaIndexTool**               | 启用 LlamaIndex 工具的使用。                                                           |
| **MDXSearchTool**                | 专为在 Markdown (MDX) 文件中搜索而设计的 RAG 工具，对文档处理有用。       |
| **PDFSearchTool**                | 旨在 PDF 文档中搜索的 RAG 工具，适合处理扫描文档。    |
| **PGSearchTool**                 | 优化的 RAG 工具，用于在 PostgreSQL 数据库中搜索，适合数据库查询。                 |
| **Vision Tool**                  | 使用 DALL-E API 生成图像的工具。                  |
| **RagTool**                      | 能够处理各种数据源和类型的通用 RAG 工具。                 |
| **ScrapeElementFromWebsiteTool** | 支持从网站抓取特定元素，对定向数据提取有用。         |
| **ScrapeWebsiteTool**            | 便于抓取整个网站，适合全面数据收集。                 |
| **WebsiteSearchTool**            | 用于搜索网站内容的 RAG 工具，针对网络数据提取进行了优化。                   |
| **XMLSearchTool**                | 设计用于在 XML 文件中搜索的 RAG 工具，适合结构化数据格式。      |
| **YoutubeChannelSearchTool**     | 用于在 YouTube 频道中搜索的 RAG 工具，对视频内容分析有用。           |
| **YoutubeVideoSearchTool**       | 旨在在 YouTube 视频中搜索的 RAG 工具，适合视频数据提取。          |

## 创建自己的工具

<Tip>
  开发人员可以为其智能体需求定制 `自定义工具` 或
  利用预构建选项。
</Tip>

创建 CrewAI 工具主要有两种方式：

### 继承 `BaseTool`

```python Code theme={null}
from crewai.tools import BaseTool
from pydantic import BaseModel, Field

class MyToolInput(BaseModel):
    """MyCustomTool 的输入模式。"""
    argument: str = Field(..., description="参数的描述。")

class MyCustomTool(BaseTool):
    name: str = "我的工具名称"
    description: str = "这个工具的作用。有效使用至关重要。"
    args_schema: Type[BaseModel] = MyToolInput

    def _run(self, argument: str) -> str:
        # 您的工具逻辑在这里
        return "工具的结果"
```

## 异步工具支持

CrewAI 支持异步工具，允许您实现执行非阻塞操作的工具，如网络请求、文件 I/O 或其他异步操作，而不会阻塞主执行线程。

### 创建异步工具

您可以通过两种方式创建异步工具：

#### 1. 使用 `tool` 装饰器与异步函数

```python Code theme={null}
from crewai.tools import tool

@tool("fetch_data_async")
async def fetch_data_async(query: str) -> str:
    """根据查询异步获取数据。"""
    # 模拟异步操作
    await asyncio.sleep(1)
    return f"为 {query} 检索到的数据"
```

#### 2. 在自定义工具类中实现异步方法

```python Code theme={null}
from crewai.tools import BaseTool

class AsyncCustomTool(BaseTool):
    name: str = "async_custom_tool"
    description: str = "一个异步自定义工具"

    async def _run(self, query: str = "") -> str:
        """异步运行工具"""
        # 您的异步实现在这里
        await asyncio.sleep(1)
        return f"异步处理 {query}"
```

### 使用异步工具

异步工具在标准 Crew 工作流程和基于 Flow 的工作流程中都能无缝工作：

```python Code theme={null}
# 在标准 Crew 中
agent = Agent(role="researcher", tools=[async_custom_tool])

# 在 Flow 中
class MyFlow(Flow):
    @start()
    async def begin(self):
        crew = Crew(agents=[agent])
        result = await crew.kickoff_async()
        return result
```

CrewAI 框架自动处理同步和异步工具的执行，因此您无需担心如何以不同方式调用它们。

### 利用 `tool` 装饰器

```python Code theme={null}
from crewai.tools import tool
@tool("我的工具名称")
def my_tool(question: str) -> str:
    """清晰描述这个工具的用途，您的智能体将需要这些信息来使用它。"""
    # 函数逻辑在这里
    return "来自您的自定义工具的结果"
```

### 自定义缓存机制

<Tip>
  工具可以选择实现 `cache_function` 来微调缓存
  行为。此函数根据特定条件确定何时缓存结果，
  提供对缓存逻辑的细粒度控制。
</Tip>

```python Code theme={null}
from crewai.tools import tool

@tool
def multiplication_tool(first_number: int, second_number: int) -> str:
    """当您需要将两个数字相乘时使用。"""
    return first_number * second_number

def cache_func(args, result):
    # 在这种情况下，我们仅在结果是 2 的倍数时缓存
    cache = result % 2 == 0
    return cache

multiplication_tool.cache_function = cache_func

writer1 = Agent(
        role="Writer",
        goal="You write lessons of math for kids.",
        backstory="You're an expert in writing and you love to teach kids but you know nothing of math.",
        tools=[multiplication_tool],
        allow_delegation=False,
    )
    #...
```

## 结论

工具在扩展 CrewAI 智能体能力方面起着关键作用，使它们能够执行广泛的任务并有效协作。
在使用 CrewAI 构建解决方案时，利用自定义和现有工具来增强您的智能体并提升 AI 生态系统。考虑利用错误处理、
缓存机制和工具参数的灵活性来优化您的智能体性能和能力。