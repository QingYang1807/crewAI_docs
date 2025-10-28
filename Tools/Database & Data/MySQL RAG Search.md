# MySQL RAG 搜索

> `MySQLSearchTool` 旨在搜索 MySQL 数据库并返回最相关的结果。

## 概述

该工具旨在促进 MySQL 数据库表内的语义搜索。利用 RAG（检索与生成）技术，MySQLSearchTool 为用户提供了查询数据库表内容的有效方式，专门针对 MySQL 数据库定制。它通过语义搜索查询简化了查找相关数据的过程，使其成为需要在 MySQL 数据库中对大型数据集执行高级查询的用户的宝贵资源。

## 安装

要安装 `crewai_tools` 包并使用 MySQLSearchTool，请在您的终端中执行以下命令：

```shell  theme={null}
pip install 'crewai[tools]'
```

## 示例

以下是一个展示如何使用 MySQLSearchTool 在 MySQL 数据库中的表上进行语义搜索的示例：

```python Code theme={null}
from crewai_tools import MySQLSearchTool

# 使用数据库 URI 和目标表名称初始化工具
tool = MySQLSearchTool(
    db_uri='mysql://user:password@localhost:3306/mydatabase',
    table_name='employees'
)
```

## 参数

MySQLSearchTool 需要以下参数才能运行：

* `db_uri`：表示要查询的 MySQL 数据库 URI 的字符串。此参数为必填项，必须包含必要的身份验证详细信息和数据库位置。
* `table_name`：指定数据库中将执行语义搜索的表名称的字符串。此参数为必填项。

## 自定义模型和嵌入

默认情况下，该工具使用 OpenAI 进行嵌入和摘要。要自定义模型，您可以使用配置字典，如下所示：

```python Code theme={null}
tool = MySQLSearchTool(
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
            provider="google",
            config=dict(
                model="models/embedding-001",
                task_type="retrieval_document",
                # title="Embeddings",
            ),
        ),
    )
)
```