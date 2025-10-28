# 工具概述

> 探索 CrewAI 丰富的 40+ 工具库，为您的 AI 智能体赋能

CrewAI 提供了一个庞大的预构建工具库，以增强您智能体的能力。从文件处理到网页抓取，从数据库查询到 AI 服务 - 我们已为您全面覆盖。

## **工具类别**

<CardGroup cols={2}>
  <Card title="文件与文档" icon="folder-open" href="/en/tools/file-document/overview" color="#3B82F6">
    读取、写入和搜索各种文件格式，包括 PDF、DOCX、JSON、CSV 等。非常适合文档处理工作流。
  </Card>

  <Card title="网页抓取与浏览" icon="globe" href="/en/tools/web-scraping/overview" color="#10B981">
    使用 Firecrawl、Selenium 等工具从网站提取数据，自动化浏览器交互，以及大规模抓取内容。
  </Card>

  <Card title="搜索与研究" icon="magnifying-glass" href="/en/tools/search-research/overview" color="#F59E0B">
    执行网络搜索，查找代码仓库，研究 YouTube 内容，并在整个互联网上发现信息。
  </Card>

  <Card title="数据库与数据" icon="database" href="/en/tools/database-data/overview" color="#8B5CF6">
    连接到 SQL 数据库、向量存储和数据仓库。查询 MySQL、PostgreSQL、Snowflake、Qdrant 和 Weaviate。
  </Card>

  <Card title="人工智能与机器学习" icon="brain" href="/en/tools/ai-ml/overview" color="#EF4444">
    使用 DALL-E 生成图像，处理视觉任务，集成 LangChain，构建 RAG 系统，以及利用代码解释器。
  </Card>

  <Card title="云与存储" icon="cloud" href="/en/tools/cloud-storage/overview" color="#06B6D4">
    与云服务交互，包括 AWS S3、Amazon Bedrock 以及其他云存储和 AI 服务。
  </Card>

  <Card title="自动化" icon="bolt" href="/en/tools/automation/overview" color="#84CC16">
    使用 Apify、Composio 和其他平台自动化工作流，将您的智能体与外部服务连接。
  </Card>

  <Card title="集成" icon="plug" href="/en/tools/tool-integrations/overview" color="#0891B2">
    将 CrewAI 与 Amazon Bedrock 和 CrewAI 自动化工具包等外部系统集成。
  </Card>
</CardGroup>

## **快速访问**

需要特定工具？以下是一些热门选择：

<CardGroup cols={3}>
  <Card title="RAG 工具" icon="image" href="/en/tools/ai-ml/ragtool">
    实现检索增强生成
  </Card>

  <Card title="Serper 开发者工具" icon="book-atlas" href="/en/tools/search-research/serperdevtool">
    Google 搜索 API
  </Card>

  <Card title="文件读取工具" icon="file" href="/en/tools/file-document/filereadtool">
    读取任何文件类型
  </Card>

  <Card title="网站抓取工具" icon="globe" href="/en/tools/web-scraping/scrapewebsitetool">
    提取网页内容
  </Card>

  <Card title="代码解释器" icon="code" href="/en/tools/ai-ml/codeinterpretertool">
    执行 Python 代码
  </Card>

  <Card title="S3 读取器" icon="cloud" href="/en/tools/cloud-storage/s3readertool">
    访问 AWS S3 文件
  </Card>
</CardGroup>

## **开始使用**

要在您的 CrewAI 项目中使用任何工具：

1. 在您的 crew 配置中**导入**该工具
2. 将其**添加**到您的智能体工具列表中
3. **配置**任何必需的 API 密钥或设置

```python  theme={null}
from crewai_tools import FileReadTool, SerperDevTool

# 将工具添加到您的智能体
agent = Agent(
    role="研究分析师",
    tools=[FileReadTool(), SerperDevTool()],
    # ... 其他配置
)
```

## **最大使用次数**

您可以为工具设置最大使用次数，以防止其被使用超过特定次数。
默认情况下，最大使用次数是无限制的。

```python  theme={null}
from crewai_tools import FileReadTool

tool = FileReadTool(max_usage_count=5, ...)
```

准备好探索了吗？从上方选择一个类别，发现适合您用例的工具！