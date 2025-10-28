# 知识库

> CrewAI中的知识库是什么以及如何使用它。

## 概述

CrewAI中的知识库是一个强大的系统，允许AIAgent在执行任务期间访问和利用外部信息源。
可以将其视为为Agent提供一个可供工作时查阅的参考图书馆。

使用知识库的主要优势：
* 通过特定领域的信息增强Agent能力
* 通过真实数据支持决策
* 在对话中保持上下文连贯
* 将回答基于事实信息

## 快速入门示例


对于基于文件的知识源，请确保将文件放在项目根目录下的`knowledge`文件夹中。
创建源时，使用相对于`knowledge`目录的路径。


### 向量存储（RAG）客户端配置

CrewAI 提供了一个与提供商无关的向量存储 RAG 客户端抽象。默认提供商是 ChromaDB，同时支持 Qdrant。您可以使用配置工具切换提供商。

目前支持的提供商：

* ChromaDB（默认）
* Qdrant

```python
from crewai.rag.config.utils import set_rag_config, get_rag_client, clear_rag_config

# ChromaDB（默认）
from crewai.rag.chromadb.config import ChromaDBConfig
set_rag_config(ChromaDBConfig())
chromadb_client = get_rag_client()

# Qdrant
from crewai.rag.qdrant.config import QdrantConfig
set_rag_config(QdrantConfig())
qdrant_client = get_rag_client()

# 示例操作（任何提供商都使用相同的 API）
client = qdrant_client  # 或 chromadb_client
client.create_collection(collection_name="docs")
client.add_documents(
    collection_name="docs",
    documents=[{"id": "1", "content": "CrewAI 使协作式 AI 智能体成为可能。"}],
)
results = client.search(collection_name="docs", query="协作式智能体", limit=3)

clear_rag_config()  # 可选重置
```

此 RAG 客户端与 Knowledge 的内置存储是分开的。当您需要直接控制向量存储或自定义检索管道时，请使用它。

### 基本字符串知识示例

```python
from crewai import Agent, Task, Crew, Process, LLM
from crewai.knowledge.source.string_knowledge_source import StringKnowledgeSource

# 创建知识源
content = "用户名叫 John。他 30 岁，住在旧金山。"
string_source = StringKnowledgeSource(content=content)

# 创建温度为 0 的 LLM 以确保确定性输出
llm = LLM(model="gpt-4o-mini", temperature=0)

# 创建带有知识存储的智能体
agent = Agent(
    role="关于用户",
    goal="您了解关于用户的一切。",
    backstory="您是理解人们及其偏好的大师。",
    verbose=True,
    allow_delegation=False,
    llm=llm,
)

task = Task(
    description="回答关于用户的以下问题：{question}",
    expected_output="对问题的回答。",
    agent=agent,
)

crew = Crew(
    agents=[agent],
    tasks=[task],
    verbose=True,
    process=Process.sequential,
    knowledge_sources=[string_source], # 通过在此处添加源来启用知识
)

result = crew.kickoff(inputs={"question": "John 住在哪个城市，他多大了？"})
```

### 网页内容知识示例

要使以下示例工作，您需要安装 `docling`：`uv add docling`

```python
from crewai import LLM, Agent, Crew, Process, Task
from crewai.knowledge.source.crew_docling_source import CrewDoclingSource

# 从网页内容创建知识源
content_source = CrewDoclingSource(
    file_paths=[
        "https://lilianweng.github.io/posts/2024-11-28-reward-hacking",
        "https://lilianweng.github.io/posts/2024-07-07-hallucination",
    ],
)

# 创建温度为 0 的 LLM 以确保确定性输出
llm = LLM(model="gpt-4o-mini", temperature=0)

# 创建带有知识存储的智能体
agent = Agent(
    role="关于论文",
    goal="您了解关于论文的一切。",
    backstory="您是理解论文及其内容的大师。",
    verbose=True,
    allow_delegation=False,
    llm=llm,
)

task = Task(
    description="回答关于论文的以下问题：{question}",
    expected_output="对问题的回答。",
    agent=agent,
)

crew = Crew(
    agents=[agent],
    tasks=[task],
    verbose=True,
    process=Process.sequential,
    knowledge_sources=[content_source],
)

result = crew.kickoff(
    inputs={"question": "奖励黑客论文是关于什么的？请务必提供来源。"}
)
```

## 支持的知识源

CrewAI 开箱即用地支持各种类型的知识源：

<CardGroup cols={2}>
  <Card title="文本源" icon="text">
    * 原始字符串
    * 文本文件 (.txt)
    * PDF 文档
  </Card>

  <Card title="结构化数据" icon="table">
    * CSV 文件
    * Excel 电子表格
    * JSON 文档
  </Card>
</CardGroup>

### 文本文件知识源

```python 
from crewai.knowledge.source.text_file_knowledge_source import TextFileKnowledgeSource

text_source = TextFileKnowledgeSource(
    file_paths=["document.txt", "another.txt"]
)
```

### PDF 知识源

```python 
from crewai.knowledge.source.pdf_knowledge_source import PDFKnowledgeSource

pdf_source = PDFKnowledgeSource(
    file_paths=["document.pdf", "another.pdf"]
)
```

### CSV 知识源

```python 
from crewai.knowledge.source.csv_knowledge_source import CSVKnowledgeSource

csv_source = CSVKnowledgeSource(
    file_paths=["data.csv"]
)
```

### Excel 知识源

```python 
from crewai.knowledge.source.excel_knowledge_source import ExcelKnowledgeSource

excel_source = ExcelKnowledgeSource(
    file_paths=["spreadsheet.xlsx"]
)
```

### JSON 知识源

```python 
from crewai.knowledge.source.json_knowledge_source import JSONKnowledgeSource

json_source = JSONKnowledgeSource(
    file_paths=["data.json"]
)
```

请确保您创建了 ./knowledge 文件夹。所有源文件（如 .txt、.pdf、.xlsx、.json）都应放置在此文件夹中以进行集中管理。

## 智能体与团队知识：完整指南

 **理解知识级别**：CrewAI 支持智能体级别和团队级别的知识。本节将明确说明每种级别的工作方式、初始化时间，并解决关于依赖关系的常见误解。

### 知识初始化的实际工作原理

使用知识时会发生以下情况：

#### 智能体级别知识（独立）

```python 
from crewai import Agent, Task, Crew
from crewai.knowledge.source.string_knowledge_source import StringKnowledgeSource

# 带有自己的知识的智能体 - 不需要团队知识
specialist_knowledge = StringKnowledgeSource(
    content="仅适用于此智能体的专业技术信息"
)

specialist_agent = Agent(
    role="技术专家",
    goal="提供专业技术知识",
    backstory="专业技术领域专家",
    knowledge_sources=[specialist_knowledge]  # 智能体特定知识
)

task = Task(
    description="回答技术问题",
    agent=specialist_agent,
    expected_output="技术答案"
)

# 不需要团队级别的知识
crew = Crew(
    agents=[specialist_agent],
    tasks=[task]
)

result = crew.kickoff()  # 智能体知识独立工作
```

#### `crew.kickoff()` 期间发生的情况

当您调用 `crew.kickoff()` 时，确切的执行顺序是：

```python 
# 在执行期间
for agent in self.agents:
    agent.crew = self  # 智能体获取对团队的引用
    agent.set_knowledge(crew_embedder=self.embedder)  # 智能体知识初始化
    agent.create_agent_executor()
```

#### 存储独立性

每个知识级别使用独立的存储集合：

```python 
# 智能体知识存储
agent_collection_name = agent.role  # 例如 "技术专家"

# 团队知识存储
crew_collection_name = "crew"

# 两者都存储在同一个 ChromaDB 实例中，但集合不同
# 路径：~/.local/share/CrewAI/{project}/knowledge/
#   ├── crew/                    # 团队知识集合
#   ├── 技术专家/                # 智能体知识集合
#   └── 另一个智能体角色/        # 另一个智能体的集合
```

### 完整的工作示例

#### 示例 1：仅智能体知识

```python 
from crewai import Agent, Task, Crew
from crewai.knowledge.source.string_knowledge_source import StringKnowledgeSource

# 智能体特定知识
agent_knowledge = StringKnowledgeSource(
    content="仅此智能体需要的智能体特定信息"
)

agent = Agent(
    role="专家",
    goal="使用专门知识",
    backstory="具有特定知识的专家",
    knowledge_sources=[agent_knowledge],
    embedder={  # 智能体可以有自己的嵌入器
        "provider": "openai",
        "config": {"model": "text-embedding-3-small"}
    }
)

task = Task(
    description="使用您的专门知识回答",
    agent=agent,
    expected_output="基于智能体知识的答案"
)

# 不需要团队知识
crew = Crew(agents=[agent], tasks=[task])
result = crew.kickoff()  # 完美工作
```

#### 示例 2：智能体和团队知识

```python 
# 团队范围的知识（所有智能体共享）
crew_knowledge = StringKnowledgeSource(
    content="公司政策和所有智能体需要的通用信息"
)

# 智能体特定知识
specialist_knowledge = StringKnowledgeSource(
    content="仅专家需要的技术规格"
)

specialist = Agent(
    role="技术专家",
    goal="提供专业技术知识",
    backstory="技术专家",
    knowledge_sources=[specialist_knowledge]  # 智能体特定
)

generalist = Agent(
    role="通用助理", 
    goal="提供一般协助",
    backstory="通用助手"
    # 没有智能体特定知识
)

crew = Crew(
    agents=[specialist, generalist],
    tasks=[...],
    knowledge_sources=[crew_knowledge]  # 团队范围知识
)

# 结果：
# - specialist 获得：crew_knowledge + specialist_knowledge
# - generalist 获得：仅 crew_knowledge
```

#### 示例 3：具有不同知识的多个智能体

```python 
# 不同智能体的不同知识
sales_knowledge = StringKnowledgeSource(content="销售流程和定价")
tech_knowledge = StringKnowledgeSource(content="技术文档")
support_knowledge = StringKnowledgeSource(content="支持流程")

sales_agent = Agent(
    role="销售代表",
    knowledge_sources=[sales_knowledge],
    embedder={"provider": "openai", "config": {"model": "text-embedding-3-small"}}
)

tech_agent = Agent(
    role="技术专家", 
    knowledge_sources=[tech_knowledge],
    embedder={"provider": "ollama", "config": {"model": "mxbai-embed-large"}}
)

support_agent = Agent(
    role="支持专家",
    knowledge_sources=[support_knowledge]
    # 将使用团队嵌入器作为后备
)

crew = Crew(
    agents=[sales_agent, tech_agent, support_agent],
    tasks=[...],
    embedder={  # 没有自己的嵌入器的智能体的后备嵌入器
        "provider": "google",
        "config": {"model": "text-embedding-004"}
    }
)

# 每个智能体只获得其特定知识
# 每个智能体可以使用不同的嵌入提供商
```


与使用工具从向量数据库中检索不同，预加载知识的智能体将不需要检索角色或任务。
只需添加您的智能体或团队运行所需的相关知识源即可。

知识源可以在智能体级别或团队级别添加。
团队级别的知识源将被团队中的**所有智能体**使用。
智能体级别的知识源将被预加载知识的**特定智能体**使用。


## 知识配置

您可以为团队或智能体配置知识设置。

```python
from crewai.knowledge.knowledge_config import KnowledgeConfig

knowledge_config = KnowledgeConfig(results_limit=10, score_threshold=0.5)

agent = Agent(
    ...
    knowledge_config=knowledge_config
)
```

`results_limit`：要返回的相关文档数量。默认为 3。
`score_threshold`：文档被视为相关的最低分数。默认为 0.35。


## 支持的知识参数

<ParamField body="sources" type="List[BaseKnowledgeSource]" required="Yes">
  提供要存储和查询的内容的知识源列表。可以包括 PDF、CSV、Excel、JSON、文本文件或字符串内容。
</ParamField>

<ParamField body="collection_name" type="str">
  存储知识的集合名称。用于标识不同的知识集。如果未提供，默认为 "knowledge"。
</ParamField>

<ParamField body="storage" type="Optional[KnowledgeStorage]">
  用于管理知识存储和检索方式的自定义存储配置。如果未提供，将创建默认存储。
</ParamField>

## 知识存储透明度

**理解知识存储**：CrewAI 自动将知识源存储在平台特定的目录中，使用 ChromaDB 进行向量存储。了解这些位置和默认值有助于生产部署、调试和存储管理。


### CrewAI 存储知识文件的位置

默认情况下，CrewAI 使用与内存相同的存储系统，将知识存储在平台特定的目录中：

#### 按平台划分的默认存储位置

**macOS：**

```
~/Library/Application Support/CrewAI/{project_name}/
└── knowledge/                    # 知识 ChromaDB 文件
    ├── chroma.sqlite3           # ChromaDB 元数据
    ├── {collection_id}/         # 向量嵌入
    └── knowledge_{collection}/  # 命名集合
```

**Linux：**

```
~/.local/share/CrewAI/{project_name}/
└── knowledge/
    ├── chroma.sqlite3
    ├── {collection_id}/
    └── knowledge_{collection}/
```

**Windows：**

```
C:\Users\{username}\AppData\Local\CrewAI\{project_name}\
└── knowledge\
    ├── chroma.sqlite3
    ├── {collection_id}\
    └── knowledge_{collection}\
```

### 查找知识存储位置

要准确查看 CrewAI 存储知识文件的位置：

```python 
from crewai.utilities.paths import db_storage_path
import os

# 获取知识存储路径
knowledge_path = os.path.join(db_storage_path(), "knowledge")
print(f"知识存储位置：{knowledge_path}")

# 列出知识集合和文件
if os.path.exists(knowledge_path):
    print("\n知识存储内容：")
    for item in os.listdir(knowledge_path):
        item_path = os.path.join(knowledge_path, item)
        if os.path.isdir(item_path):
            print(f"📁 集合：{item}/")
            # 显示集合内容
            try:
                for subitem in os.listdir(item_path):
                    print(f"   └── {subitem}")
            except PermissionError:
                print(f"   └── （权限被拒绝）")
        else:
            print(f"📄 {item}")
else:
    print("尚未找到知识存储。")
```

### 控制知识存储位置

#### 选项 1：环境变量（推荐）

```python 
import os
from crewai import Crew

# 为所有 CrewAI 数据设置自定义存储位置
os.environ["CREWAI_STORAGE_DIR"] = "./my_project_storage"

# 所有知识现在将存储在 ./my_project_storage/knowledge/ 中
crew = Crew(
    agents=[...],
    tasks=[...],
    knowledge_sources=[...]
)
```

#### 选项 2：自定义知识存储

```python 
from crewai.knowledge.storage.knowledge_storage import KnowledgeStorage
from crewai.knowledge.source.string_knowledge_source import StringKnowledgeSource

# 创建具有特定嵌入器的自定义存储
custom_storage = KnowledgeStorage(
    embedder={
        "provider": "ollama",
        "config": {"model": "mxbai-embed-large"}
    },
    collection_name="my_custom_knowledge"
)

# 与知识源一起使用
knowledge_source = StringKnowledgeSource(
    content="您的知识内容在这里"
)
knowledge_source.storage = custom_storage
```

#### 选项 3：项目特定知识存储

```python 
import os
from pathlib import Path

# 将知识存储在项目目录中
project_root = Path(__file__).parent
knowledge_dir = project_root / "knowledge_storage"

os.environ["CREWAI_STORAGE_DIR"] = str(knowledge_dir)

# 现在所有知识将存储在您的项目目录中
```

### 默认嵌入提供商行为

**默认嵌入提供商**：CrewAI 默认使用 OpenAI 嵌入（`text-embedding-3-small`）进行知识存储，即使使用不同的 LLM 提供商也是如此。您可以轻松自定义此设置以匹配您的设置。

#### 理解默认行为

```python 
from crewai import Agent, Crew, LLM
from crewai.knowledge.source.string_knowledge_source import StringKnowledgeSource

# 当使用 Claude 作为您的 LLM 时...
agent = Agent(
    role="研究员",
    goal="研究主题",
    backstory="专业研究员",
    llm=LLM(provider="anthropic", model="claude-3-sonnet")  # 使用 Claude
)

# CrewAI 仍然默认使用 OpenAI 嵌入进行知识存储
# 这确保了一致性，但可能不符合您的 LLM 提供商偏好
knowledge_source = StringKnowledgeSource(content="研究数据...")

crew = Crew(
    agents=[agent],
    tasks=[...],
    knowledge_sources=[knowledge_source]
    # 默认：即使使用 Claude LLM 也使用 OpenAI 嵌入
)
```

#### 自定义知识嵌入提供商

```python 
# 选项 1：使用 Voyage AI（Claude 用户推荐）
crew = Crew(
    agents=[agent],
    tasks=[...],
    knowledge_sources=[knowledge_source],
    embedder={
        "provider": "voyageai",  # Claude 用户推荐
        "config": {
            "api_key": "your-voyage-api-key",
            "model": "voyage-3"  # 或 "voyage-3-large" 获得最佳质量
        }
    }
)

# 选项 2：使用本地嵌入（无外部 API 调用）
crew = Crew(
    agents=[agent],
    tasks=[...],
    knowledge_sources=[knowledge_source],
    embedder={
        "provider": "ollama",
        "config": {
            "model": "mxbai-embed-large",
            "url": "http://localhost:11434/api/embeddings"
        }
    }
)

# 选项 3：智能体级别嵌入自定义
agent = Agent(
    role="研究员",
    goal="研究主题",
    backstory="专业研究员",
    knowledge_sources=[knowledge_source],
    embedder={
        "provider": "google",
        "config": {
            "model": "models/text-embedding-004",
            "api_key": "your-google-key"
        }
    }
)
```

#### 配置 Azure OpenAI 嵌入

使用 Azure OpenAI 嵌入时：

1. 首先确保您在 Azure 平台上部署了嵌入模型
2. 然后您需要使用以下配置：

```python 
agent = Agent(
    role="研究员",
    goal="研究主题",
    backstory="专业研究员",
    knowledge_sources=[knowledge_source],
    embedder={
        "provider": "azure",
        "config": {
            "api_key": "your-azure-api-key",
            "model": "text-embedding-ada-002", # 更改为您正在使用且已在 Azure 中部署的模型
            "api_base": "https://your-azure-endpoint.openai.azure.com/",
            "api_version": "2024-02-01"
        }
    }
)
```

## 高级功能

### 查询重写

CrewAI 实现了一个智能查询重写机制，以优化知识检索。当智能体需要搜索知识源时，原始任务提示会自动转换为更有效的搜索查询。

#### 查询重写如何工作

1. 当智能体执行具有可用知识源的任务时，`_get_knowledge_search_query` 方法被触发
2. 智能体的 LLM 用于将原始任务提示转换为优化的搜索查询
3. 然后使用此优化的查询从知识源检索相关信息

#### 查询重写的好处

<CardGroup cols={2}>
  <Card title="提高检索准确性" icon="bullseye-arrow">
    通过专注于关键概念并删除不相关的内容，查询重写有助于检索更相关的信息。
  </Card>

  <Card title="上下文感知" icon="brain">
    重写的查询设计为对向量数据库检索更加具体和上下文感知。
  </Card>
</CardGroup>

#### 示例

```python 
# 原始任务提示
task_prompt = "回答关于用户最喜欢的电影的以下问题：John 上周看了什么电影？请以 JSON 格式回答。"

# 在幕后，可能会被重写为：
rewritten_query = "John 上周看了什么电影？"
```

重写的查询更专注于核心信息需求，并删除了关于输出格式的不相关说明。


机制是完全自动的，不需要用户进行任何配置。智能体的 LLM 用于执行查询重写，因此使用更强大的 LLM 可以提高重写查询的质量。

### 知识事件

CrewAI 在知识检索过程中发出事件，您可以使用事件系统监听这些事件。这些事件允许您监控、调试和分析智能体如何检索和使用知识。

#### 可用的知识事件

* **KnowledgeRetrievalStartedEvent**：当智能体开始从知识源检索知识时发出
* **KnowledgeRetrievalCompletedEvent**：知识检索完成时发出，包括使用的查询和检索的内容
* **KnowledgeQueryStartedEvent**：当向知识源开始查询时发出
* **KnowledgeQueryCompletedEvent**：查询成功完成时发出
* **KnowledgeQueryFailedEvent**：当查询知识源失败时发出
* **KnowledgeSearchQueryFailedEvent**：当搜索查询失败时发出

#### 示例：监控知识检索

```python 
from crewai.events import (
    KnowledgeRetrievalStartedEvent,
    KnowledgeRetrievalCompletedEvent,
    BaseEventListener,
)

class KnowledgeMonitorListener(BaseEventListener):
    def setup_listeners(self, crewai_event_bus):
        @crewai_event_bus.on(KnowledgeRetrievalStartedEvent)
        def on_knowledge_retrieval_started(source, event):
            print(f"智能体 '{event.agent.role}' 开始检索知识")
          
        @crewai_event_bus.on(KnowledgeRetrievalCompletedEvent)
        def on_knowledge_retrieval_completed(source, event):
            print(f"智能体 '{event.agent.role}' 完成知识检索")
            print(f"查询：{event.query}")
            print(f"检索到 {len(event.retrieved_knowledge)} 个知识块")

# 创建监听器实例
knowledge_monitor = KnowledgeMonitorListener()
```

有关使用事件的更多信息，请参见[事件监听器](https://docs.crewai.com/concepts/event-listener)文档。

### 自定义知识源

CrewAI 允许您通过扩展 `BaseKnowledgeSource` 类为任何类型的数据创建自定义知识源。让我们创建一个实际的示例，获取和处理太空新闻文章。

#### 太空新闻知识源示例

<CodeGroup>
  ```python
  from crewai import Agent, Task, Crew, Process, LLM
  from crewai.knowledge.source.base_knowledge_source import BaseKnowledgeSource
  import requests
  from datetime import datetime
  from typing import Dict, Any
  from pydantic import BaseModel, Field

  class SpaceNewsKnowledgeSource(BaseKnowledgeSource):
      """从太空新闻 API 获取数据的知识源。"""

      api_endpoint: str = Field(description="API 端点 URL")
      limit: int = Field(default=10, description="要获取的文章数量")

      def load_content(self) -> Dict[Any, str]:
          """获取并格式化太空新闻文章。"""
          try:
              response = requests.get(
                  f"{self.api_endpoint}?limit={self.limit}"
              )
              response.raise_for_status()

              data = response.json()
              articles = data.get('results', [])

              formatted_data = self.validate_content(articles)
              return {self.api_endpoint: formatted_data}
          except Exception as e:
              raise ValueError(f"获取太空新闻失败：{str(e)}")

      def validate_content(self, articles: list) -> str:
          """将文章格式化为可读文本。"""
          formatted = "太空新闻文章：\n\n"
          for article in articles:
              formatted += f"""
                  标题：{article['title']}
                  发布时间：{article['published_at']}
                  摘要：{article['summary']}
                  新闻网站：{article['news_site']}
                  网址：{article['url']}
                  -------------------"""
          return formatted

      def add(self) -> None:
          """处理并存储文章。"""
          content = self.load_content()
          for _, text in content.items():
              chunks = self._chunk_text(text)
              self.chunks.extend(chunks)

          self._save_documents()

  # 创建知识源
  recent_news = SpaceNewsKnowledgeSource(
      api_endpoint="https://api.spaceflightnewsapi.net/v4/articles",
      limit=10,
  )

  # 创建专业智能体
  space_analyst = Agent(
      role="太空新闻分析师",
      goal="准确全面地回答关于太空新闻的问题",
      backstory="""您是一位航天行业分析师，专长于太空探索、
      卫星技术和航天行业趋势。您擅长回答关于
      太空新闻的问题并提供详细、准确的信息。""",
      knowledge_sources=[recent_news],
      llm=LLM(model="gpt-4", temperature=0.0)
  )

  # 创建处理用户问题的任务
  analysis_task = Task(
      description="回答这个关于太空新闻的问题：{user_question}",
      expected_output="基于最近太空新闻文章的详细回答",
      agent=space_analyst
  )

  # 创建并运行团队
  crew = Crew(
      agents=[space_analyst],
      tasks=[analysis_task],
      verbose=True,
      process=Process.sequential
  )

  # 示例用法
  result = crew.kickoff(
      inputs={"user_question": "太空探索的最新发展是什么？"}
  )
  ```

  ```输出
  # 智能体：太空新闻分析师
  ## 任务：回答这个关于太空新闻的问题：太空探索的最新发展是什么？


  # 智能体：太空新闻分析师
  ## 最终答案：
  基于最近的太空新闻文章，太空探索的最新发展包括以下内容：

  1. SpaceX 已获得最终监管批准，可以推进第二次集成的 Starship/Super Heavy 发射，计划最早于 2023 年 11 月 17 日上午进行。这是 SpaceX 雄心勃勃的太空探索和殖民计划的重要一步。[来源：SpaceNews](https://spacenews.com/starship-cleared-for-nov-17-launch/)

  2. SpaceX 已通知美国联邦通信委员会（FCC），计划开始发射其第一代下一代 Starlink Gen2 卫星。这代表了 Starlink 卫星互联网服务的主要升级，旨在为全球提供高速互联网访问。[来源：Teslarati](https://www.teslarati.com/spacex-first-starlink-gen2-satellite-launch-2022/)

  3. AI 初创公司 Synthetaic 在 B 轮融资中筹集了 1500 万美元。该公司使用人工智能分析来自太空和空中传感器的数据，这可能对太空探索和卫星技术有重要应用。[来源：SpaceNews](https://spacenews.com/ai-startup-synthetaic-raises-15-million-in-series-b-funding/)

  4. 太空军已在美国印太司令部内正式成立一个单位，标志着在印太地区的永久存在。这可能对太空安全和地缘政治具有重要意义。[来源：SpaceNews](https://spacenews.com/space-force-establishes-permanent-presence-in-indo-pacific-region/)

  5. Slingshot Aerospace 是一家太空跟踪和数据分析公司，正在扩展其地基光学望远镜网络，以增加对低地球轨道的覆盖。这可能会提高我们跟踪和分析低地球轨道物体（包括卫星和太空碎片）的能力。[来源：SpaceNews](https://spacenews.com/slingshots-space-tracking-network-to-extend-coverage-of-low-earth-orbit/)

  6. 中国国家自然科学基金委员会已经制定了一个为期五年的项目，供研究人员研究超大型航天器的组装。这可能导致航天器技术和太空探索能力的重大进步。[来源：SpaceNews](https://spacenews.com/china-researching-challenges-of-kilometer-scale-ultra-large-spacecraft/)

  7. 斯坦福大学的 AEroSpace 自主研究中心（CAESAR）专注于航天器自主性。该中心于 2024 年 5 月 22 日举办了启动活动，突出了其寻求促进的行业、学术界和政府合作。这可能导致自主航天器技术的重大进步。[来源：SpaceNews](https://spacenews.com/stanford-center-focuses-on-spacecraft-autonomy/)
  ```
</CodeGroup>

## 调试和故障排除

### 调试知识问题

#### 检查智能体知识初始化

```python 
from crewai import Agent, Crew, Task
from crewai.knowledge.source.string_knowledge_source import StringKnowledgeSource

knowledge_source = StringKnowledgeSource(content="测试知识")

agent = Agent(
    role="测试智能体",
    goal="测试知识",
    backstory="测试",
    knowledge_sources=[knowledge_source]
)

crew = Crew(agents=[agent], tasks=[Task(...)])

# 执行前 - 知识未初始化
print(f"执行前 - 智能体知识：{getattr(agent, 'knowledge', None)}")

crew.kickoff()

# 执行后 - 知识已初始化
print(f"执行后 - 智能体知识：{agent.knowledge}")
print(f"智能体知识集合：{agent.knowledge.storage.collection_name}")
print(f"源数量：{len(agent.knowledge.sources)}")
```

#### 验证知识存储位置

```python 
import os
from crewai.utilities.paths import db_storage_path

# 检查存储结构
storage_path = db_storage_path()
knowledge_path = os.path.join(storage_path, "knowledge")

if os.path.exists(knowledge_path):
    print("找到知识集合：")
    for collection in os.listdir(knowledge_path):
        collection_path = os.path.join(knowledge_path, collection)
        if os.path.isdir(collection_path):
            print(f"  - {collection}/")
            # 显示集合内容
            for item in os.listdir(collection_path):
                print(f"    └── {item}")
```

#### 测试知识检索

```python 
# 测试智能体知识检索
if hasattr(agent, 'knowledge') and agent.knowledge:
    test_query = ["测试查询"]
    results = agent.knowledge.query(test_query)
    print(f"智能体知识结果：找到 {len(results)} 个文档")
  
    # 测试团队知识检索（如果存在）
    if hasattr(crew, 'knowledge') and crew.knowledge:
        crew_results = crew.query_knowledge(test_query)
        print(f"团队知识结果：找到 {len(crew_results)} 个文档")
```

#### 检查知识集合

```python 
import chromadb
from crewai.utilities.paths import db_storage_path
import os

# 连接到 CrewAI 的知识 ChromaDB
knowledge_path = os.path.join(db_storage_path(), "knowledge")

if os.path.exists(knowledge_path):
    client = chromadb.PersistentClient(path=knowledge_path)
    collections = client.list_collections()
  
    print("知识集合：")
    for collection in collections:
        print(f"  - {collection.name}：{collection.count()} 个文档")
      
        # 采样几个文档以验证内容
        if collection.count() > 0:
            sample = collection.peek(limit=2)
            print(f"    示例内容：{sample['documents'][0][:100]}...")
else:
    print("未找到知识存储")
```

#### 检查知识处理

```python 
from crewai.knowledge.source.string_knowledge_source import StringKnowledgeSource

# 创建测试知识源
test_source = StringKnowledgeSource(
    content="用于调试的测试知识内容",
    chunk_size=100,  # 小块用于测试
    chunk_overlap=20
)

# 检查分块行为
print(f"原始内容长度：{len(test_source.content)}")
print(f"块大小：{test_source.chunk_size}")
print(f"块重叠：{test_source.chunk_overlap}")

# 处理并检查块
test_source.add()
print(f"创建的块数量：{len(test_source.chunks)}")
for i, chunk in enumerate(test_source.chunks[:3]):  # 显示前 3 个块
    print(f"块 {i+1}：{chunk[:50]}...")
```

### 常见知识存储问题

**"文件未找到"错误：**

```python 
# 确保文件位于正确的位置
from crewai.utilities.constants import KNOWLEDGE_DIRECTORY
import os

knowledge_dir = KNOWLEDGE_DIRECTORY  # 通常是 "knowledge"
file_path = os.path.join(knowledge_dir, "your_file.pdf")

if not os.path.exists(file_path):
    print(f"未找到文件：{file_path}")
    print(f"当前工作目录：{os.getcwd()}")
    print(f"预期的知识目录：{os.path.abspath(knowledge_dir)}")
```

**"嵌入维度不匹配"错误：**

```python 
# 切换嵌入提供商时会发生这种情况
# 重置知识存储以清除旧嵌入
crew.reset_memories(command_type='knowledge')

# 或使用一致的嵌入提供商
crew = Crew(
    agents=[...],
    tasks=[...],
    knowledge_sources=[...],
    embedder={"provider": "openai", "config": {"model": "text-embedding-3-small"}}
)
```

**"ChromaDB 权限被拒绝"错误：**

```bash 
# 修复存储权限
chmod -R 755 ~/.local/share/CrewAI/
```

**知识在运行之间不持久化：**

```python 
# 验证存储位置一致性
import os
from crewai.utilities.paths import db_storage_path

print("CREWAI_STORAGE_DIR：", os.getenv("CREWAI_STORAGE_DIR"))
print("计算的存储路径：", db_storage_path())
print("知识路径：", os.path.join(db_storage_path(), "knowledge"))
```

### 知识重置命令

```python 
# 仅重置智能体特定知识
crew.reset_memories(command_type='agent_knowledge')

# 重置团队和智能体知识
crew.reset_memories(command_type='knowledge')

# CLI 命令
# crewai reset-memories --agent-knowledge  # 仅智能体知识
# crewai reset-memories --knowledge        # 所有知识
```

### 清除知识

如果您需要清除 CrewAI 中存储的知识，可以使用带有 `--knowledge` 选项的 `crewai reset-memories` 命令。

```bash 命令
crewai reset-memories --knowledge
```

当您更新了知识源并希望确保智能体使用最新信息时，这很有用。

## 最佳实践

<AccordionGroup>
  <Accordion title="内容组织">
    * 为您的内容类型保持适当的块大小
    * 考虑内容重叠以保持上下文
    * 将相关信息组织到单独的知识源中
  </Accordion>

  <Accordion title="性能技巧">
    * 根据内容复杂性调整块大小
    * 配置适当的嵌入模型
    * 考虑使用本地嵌入提供商以获得更快的处理速度
  </Accordion>

  <Accordion title="一次性知识">
    * 使用 CrewAI 提供的典型文件结构，每次触发执行时都会嵌入知识源。
    * 如果知识源很大，这会导致低效和增加延迟，因为每次都会嵌入相同的数据。
    * 要解决此问题，直接初始化 knowledge 参数而不是 knowledge_sources 参数。
    * 链接到问题以获取完整思路 [Github Issue](https://github.com/crewAIInc/crewAI/issues/2755)
  </Accordion>

  <Accordion title="知识管理">
    * 将智能体级别的知识用于角色特定信息
    * 将团队级别的知识用于所有智能体需要的共享信息
    * 如果需要不同的嵌入策略，请在智能体级别设置嵌入器
    * 通过保持智能体角色描述性来使用一致的集合命名
    * 通过在执行后检查 agent.knowledge 来测试知识初始化
    * 监控存储位置以了解知识的存储位置
    * 使用正确的命令类型适当地重置知识
  </Accordion>

  <Accordion title="生产最佳实践">
    * 在生产环境中将 `CREWAI_STORAGE_DIR` 设置为已知位置
    * 选择明确的嵌入提供商以匹配您的 LLM 设置并避免 API 密钥冲突
    * 随着文档添加监控知识存储大小
    * 使用集合名称按域或目的组织知识源
    * 在您的备份和部署策略中包含知识目录
    * 为知识文件和存储目录设置适当的文件权限
    * 使用环境变量管理 API 密钥和敏感配置
  </Accordion>
</AccordionGroup>