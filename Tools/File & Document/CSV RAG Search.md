# CSV RAG 搜索

> `CSVSearchTool` 是一个强大的 RAG（检索增强生成）工具，专为在 CSV 文件内容中进行语义搜索而设计。

# `CSVSearchTool`

<Note>
  **实验性功能**：我们仍在努力改进工具，因此未来可能会出现意外行为或变更。
</Note>

## 描述

此工具用于在 CSV 文件内容中执行 RAG（检索增强生成）搜索。它允许用户在指定 CSV 文件的内容中进行语义搜索。对于从传统搜索方法可能效率低下的大型 CSV 数据集中提取信息，此功能特别有用。所有名称中包含"Search"的工具，包括 CSVSearchTool，都是为搜索不同数据源而设计的 RAG 工具。

## 安装

安装 crewai_tools 包

```shell  theme={null}
pip install 'crewai[tools]'
```

## 示例

```python Code theme={null}
from crewai_tools import CSVSearchTool

# 使用特定的 CSV 文件初始化工具。
# 此设置允许代理仅搜索给定的 CSV 文件。
tool = CSVSearchTool(csv='path/to/your/csvfile.csv')

# 或者

# 不使用特定的 CSV 文件初始化工具。
# 代理需要在运行时提供 CSV 路径。
tool = CSVSearchTool()
```

## 参数

以下参数可用于自定义 `CSVSearchTool` 的行为：

| 参数 | 类型     | 描述                                                                                                                                                                 |
| :------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **csv**  | `string` | *可选*。您要搜索的 CSV 文件的路径。如果工具初始化时没有指定 CSV 文件，则此参数是必需的；否则，它是可选的。                                |

## 自定义模型和嵌入

默认情况下，该工具使用 OpenAI 进行嵌入和总结。要自定义模型，您可以使用配置字典，如下所示：

```python Code theme={null}
from chromadb.config import Settings

tool = CSVSearchTool(
    config={
        "embedding_model": {
            "provider": "openai",
            "config": {
                "model": "text-embedding-3-small",
                # "api_key": "sk-...",
            },
        },
        "vectordb": {
            "provider": "chromadb",  # 或 "qdrant"
            "config": {
                # "settings": Settings(persist_directory="/content/chroma", allow_reset=True, is_persistent=True),
                # from qdrant_client.models import VectorParams, Distance
                # "vectors_config": VectorParams(size=384, distance=Distance.COSINE),
            }
        },
    }
)
```