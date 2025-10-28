# MongoDB 向量搜索工具

> `MongoDBVectorSearchTool` 在 MongoDB Atlas 上执行向量搜索，并提供可选的索引创建辅助功能。

# `MongoDBVectorSearchTool`

## 描述

在 MongoDB Atlas 集合上执行向量相似性查询。支持索引创建辅助功能和嵌入式文本的批量插入。

MongoDB Atlas 支持原生向量搜索。了解更多信息：
[https://www.mongodb.com/docs/atlas/atlas-vector-search/vector-search-overview/](https://www.mongodb.com/docs/atlas/atlas-vector-search/vector-search-overview/)

## 安装

使用 MongoDB 额外包安装：

```shell  theme={null}
pip install crewai-tools[mongodb]
```

或

```shell  theme={null}
uv add crewai-tools --extra mongodb
```

## 参数

### 初始化参数

* `connection_string` (字符串，必需)
* `database_name` (字符串，必需)
* `collection_name` (字符串，必需)
* `vector_index_name` (字符串，默认 `vector_index`)
* `text_key` (字符串，默认 `text`)
* `embedding_key` (字符串，默认 `embedding`)
* `dimensions` (整数，默认 `1536`)

### 运行参数

* `query` (字符串，必需)：要嵌入和搜索的自然语言查询。

## 快速开始

```python Code theme={null}
from crewai_tools import MongoDBVectorSearchTool

tool = MongoDBVectorSearchTool(
  connection_string="mongodb+srv://...",
  database_name="mydb",
  collection_name="docs",
)

print(tool.run(query="如何创建向量索引"))
```

## 索引创建辅助功能

使用 `create_vector_search_index(...)` 来配置具有正确维度和相似度的 Atlas 向量搜索索引。

## 常见问题

* 身份验证失败：确保您的 Atlas IP 访问列表允许您的运行程序，并且连接字符串包含凭据。
* 未找到索引：首先创建向量索引；名称必须与 `vector_index_name` 匹配。
* 维度不匹配：将嵌入模型的维度与 `dimensions` 对齐。

## 更多示例

### 基本初始化

```python Code theme={null}
from crewai_tools import MongoDBVectorSearchTool

tool = MongoDBVectorSearchTool(
    database_name="example_database",
    collection_name="example_collection",
    connection_string="<your_mongodb_connection_string>",
)
```

### 自定义查询配置

```python Code theme={null}
from crewai_tools import MongoDBVectorSearchConfig, MongoDBVectorSearchTool

query_config = MongoDBVectorSearchConfig(limit=10, oversampling_factor=2)
tool = MongoDBVectorSearchTool(
    database_name="example_database",
    collection_name="example_collection",
    connection_string="<your_mongodb_connection_string>",
    query_config=query_config,
    vector_index_name="my_vector_index",
)

rag_agent = Agent(
    name="rag_agent",
    role="您是一个有用的助手，可以借助 MongoDBVectorSearchTool 回答问题。",
    goal="...",
    backstory="...",
    tools=[tool],
)
```

### 预加载数据库并创建索引

```python Code theme={null}
import os
from crewai_tools import MongoDBVectorSearchTool

tool = MongoDBVectorSearchTool(
    database_name="example_database",
    collection_name="example_collection",
    connection_string="<your_mongodb_connection_string>",
)

# 从本地文件夹加载文本内容并添加到 MongoDB
texts = []
for fname in os.listdir("knowledge"):
    path = os.path.join("knowledge", fname)
    if os.path.isfile(path):
        with open(path, "r", encoding="utf-8") as f:
            texts.append(f.read())

tool.add_texts(texts)

# 创建 Atlas 向量搜索索引（例如，text-embedding-3-large 为 3072 维）
tool.create_vector_search_index(dimensions=3072)
```

## 示例

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import MongoDBVectorSearchTool

tool = MongoDBVectorSearchTool(
    connection_string="mongodb+srv://...",
    database_name="mydb",
    collection_name="docs",
)

agent = Agent(
    role="RAG 代理",
    goal="使用 MongoDB 向量搜索回答问题",
    backstory="知识检索专家",
    tools=[tool],
    verbose=True,
)

task = Task(
    description="为'索引指导'查找相关内容",
    expected_output="引用最相关匹配的简洁答案",
    agent=agent,
)

crew = Crew(
    agents=[agent], 
    tasks=[task],
    verbose=True,
)

result = crew.kickoff()
```