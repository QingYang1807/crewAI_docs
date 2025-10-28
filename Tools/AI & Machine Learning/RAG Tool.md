# RAG 工具

> `RagTool` 是一个动态知识库工具，用于使用检索增强生成来回答问题。

# `RagTool`

## 描述

`RagTool` 旨在通过利用 CrewAI 原生 RAG 系统的检索增强生成（RAG）功能来回答问题。
它提供了一个动态知识库，可以查询该知识库以从各种数据源检索相关信息。
此工具特别适用于需要访问大量信息并提供上下文相关答案的应用程序。

## 示例

以下示例演示了如何初始化该工具并将其用于不同的数据源：

```python
from crewai_tools import RagTool

# 使用默认设置创建 RAG 工具
rag_tool = RagTool()

# 从文件添加内容
rag_tool.add(data_type="file", path="path/to/your/document.pdf")

# 从网页添加内容
rag_tool.add(data_type="web_page", url="https://example.com")

# 使用 RagTool 定义代理
@agent
def knowledge_expert(self) -> Agent:
    '''
    此代理使用 RagTool 回答有关知识库的问题。
    '''
    return Agent(
        config=self.agents_config["knowledge_expert"],
        allow_delegation=False,
        tools=[rag_tool]
    )
```

## 支持的数据源

`RagTool` 可用于多种数据源，包括：

* 📰 PDF 文件
* 📊 CSV 文件
* 📃 JSON 文件
* 📝 文本
* 📁 目录/文件夹
* 🌐 HTML 网页
* 📽️ YouTube 频道
* 📺 YouTube 视频
* 📚 文档网站
* 📝 MDX 文件
* 📄 DOCX 文件
* 🧾 XML 文件
* 📬 Gmail
* 📝 GitHub 仓库
* 🐘 PostgreSQL 数据库
* 🐬 MySQL 数据库
* 🤖 Slack 对话
* 💬 Discord 消息
* 🗨️ Discourse 论坛
* 📝 Substack 新闻通讯
* 🐝 Beehiiv 内容
* 💾 Dropbox 文件
* 🖼️ 图片
* ⚙️ 自定义数据源

## 参数

`RagTool` 接受以下参数：

* **summarize**：可选。是否对检索到的内容进行总结。默认为 `False`。
* **adapter**：可选。知识库的自定义适配器。如果未提供，将使用 CrewAIRagAdapter。
* **config**：可选。底层 CrewAI RAG 系统的配置。

## 添加内容

您可以使用 `add` 方法向知识库添加内容：

```python
# 添加 PDF 文件
rag_tool.add(data_type="file", path="path/to/your/document.pdf")

# 添加网页
rag_tool.add(data_type="web_page", url="https://example.com")

# 添加 YouTube 视频
rag_tool.add(data_type="youtube_video", url="https://www.youtube.com/watch?v=VIDEO_ID")

# 添加文件目录
rag_tool.add(data_type="directory", path="path/to/your/directory")
```

## 代理集成示例

以下是如何将 `RagTool` 与 CrewAI 代理集成：

```python
from crewai import Agent
from crewai.project import agent
from crewai_tools import RagTool

# 初始化工具并添加内容
rag_tool = RagTool()
rag_tool.add(data_type="web_page", url="https://docs.crewai.com")
rag_tool.add(data_type="file", path="company_data.pdf")

# 使用 RagTool 定义代理
@agent
def knowledge_expert(self) -> Agent:
    return Agent(
        config=self.agents_config["knowledge_expert"],
        allow_delegation=False,
        tools=[rag_tool]
    )
```

## 高级配置

您可以通过提供配置字典来自定义 `RagTool` 的行为：

```python
from crewai_tools import RagTool

# 创建具有自定义配置的 RAG 工具
config = {
    "vectordb": {
        "provider": "qdrant",
        "config": {
            "collection_name": "my-collection"
        }
    },
    "embedding_model": {
        "provider": "openai",
        "config": {
            "model": "text-embedding-3-small"
        }
    }
}

rag_tool = RagTool(config=config, summarize=True)
```

## 结论

`RagTool` 提供了一种强大的方式来创建和查询来自各种数据源的知识库。通过利用检索增强生成，它使代理能够高效地访问和检索相关信息，增强它们提供准确且上下文适当的响应的能力。