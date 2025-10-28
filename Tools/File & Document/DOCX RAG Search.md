# DOCX RAG 搜索

> `DOCXSearchTool` 是一个专为在 DOCX 文档中进行语义搜索而设计的 RAG 工具。

# `DOCXSearchTool`

<Note>
  我们仍在不断改进工具，因此未来可能会出现意外的行为或变更。
</Note>

## 描述

`DOCXSearchTool` 是一个专为在 DOCX 文档中进行语义搜索而设计的 RAG 工具。
它使用户能够通过基于查询的搜索，有效地搜索和提取 DOCX 文件中的相关信息。
这个工具对于数据分析、信息管理和研究任务来说非常宝贵，
能够简化在大量文档集合中查找特定信息的过程。

## 安装

在您的终端中运行以下命令来安装 crewai_tools 包：

```shell  theme={null}
uv pip install docx2txt 'crewai[tools]'
```

## 示例

以下示例演示了如何初始化 DOCXSearchTool 以在任何 DOCX 文件的内容中搜索，或使用特定的 DOCX 文件路径。

```python Code theme={null}
from crewai_tools import DOCXSearchTool

# 初始化工具以在任何 DOCX 文件的内容中搜索
tool = DOCXSearchTool()

# 或者

# 使用特定的 DOCX 文件初始化工具，
# 这样代理只能搜索指定 DOCX 文件的内容
tool = DOCXSearchTool(docx='path/to/your/document.docx')
```

## 参数

可以使用以下参数来自定义 `DOCXSearchTool` 的行为：

| 参数 | 类型     | 描述                                                                                                                                                                                                        |
| :------- | :------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **docx** | `string` | *可选*。指定您想要搜索的 DOCX 文件路径的参数。如果在初始化时未提供，该工具允许后续指定任何 DOCX 文件的内容路径进行搜索。 |

## 自定义模型和嵌入

默认情况下，该工具使用 OpenAI 进行嵌入和摘要。要自定义模型，您可以使用配置字典，如下所示：

```python Code theme={null}
from chromadb.config import Settings

tool = DOCXSearchTool(
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