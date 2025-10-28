# PG RAG 搜索

> `PGSearchTool` 旨在搜索 PostgreSQL 数据库并返回最相关的结果。

## 概述

<Note>
  PGSearchTool 目前正在开发中。本文档概述了其预期功能和接口。
  随着开发的进展，请注意某些功能可能尚不可用或可能会发生变化。
</Note>

## 描述

PGSearchTool 被设想为一个强大的工具，用于在 PostgreSQL 数据库表内进行语义搜索。通过利用先进的检索与生成（RAG）技术，
它旨在提供一种高效的方式来查询数据库表内容，专门为 PostgreSQL 数据库量身定制。
该工具的目标是通过语义搜索查询简化查找相关数据的过程，为需要在 PostgreSQL 环境中对大量数据集进行高级查询的用户提供宝贵的资源。

## 安装

`crewai_tools` 包将在发布后包含 PGSearchTool，可以使用以下命令安装：

```shell  theme={null}
pip install 'crewai[tools]'
```

<Note>
  PGSearchTool 在当前版本的 `crewai_tools` 包中尚不可用。一旦该工具发布，此安装命令将会更新。
</Note>

## 使用示例

以下是一个建议的示例，展示了如何使用 PGSearchTool 对 PostgreSQL 数据库中的表进行语义搜索：

```python Code theme={null}
from crewai_tools import PGSearchTool

# 使用数据库 URI 和目标表名初始化工具
tool = PGSearchTool(
    db_uri='postgresql://user:password@localhost:5432/mydatabase', 
    table_name='employees'
)
```

## 参数

PGSearchTool 设计为需要以下参数才能运行：

| 参数         | 类型     | 描述                                                                                                                                                                                                          |
| :----------- | :------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **db\_uri**     | `字符串` | **必需**。表示要查询的 PostgreSQL 数据库的 URI 的字符串。此参数将是必需的，必须包含必要的身份验证详细信息和数据库位置。                                                                                       |
| **table\_name** | `字符串` | **必需**。指定数据库中将执行语义搜索的表名的字符串。此参数也将是必需的。                                                                                                                                     |

## 自定义模型和嵌入

该工具默认打算使用 OpenAI 进行嵌入和总结。用户将可以选择使用配置字典自定义模型，如下所示：

```python Code theme={null}
tool = PGSearchTool(
    config=dict(
        llm=dict(
            provider="ollama", # 或 google, openai, anthropic, llama2, ...
            config=dict(
                model="llama2",
                # temperature=0.5,
                # top_p=1,
                # stream=true,
            ),
        ),
        embedder=dict(
            provider="google", # 或 openai, ollama, ...
            config=dict(
                model="models/embedding-001",
                task_type="retrieval_document",
                # title="Embeddings",
            ),
        ),
    )
)
```