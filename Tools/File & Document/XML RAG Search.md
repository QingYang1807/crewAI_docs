# XML RAG 搜索

> `XMLSearchTool` 旨在对 XML 文件内容执行 RAG（检索增强生成）搜索。

# `XMLSearchTool`

<Note>
  我们仍在努力改进工具，因此将来可能会出现意外行为或变更。
</Note>

## 描述

XMLSearchTool 是一种尖端的 RAG 工具，专为在 XML 文件中进行语义搜索而设计。对于需要高效解析和提取 XML 内容信息的用户而言，此工具支持输入搜索查询和可选的 XML 文件路径。通过指定 XML 路径，用户可以更精确地将搜索定向到该文件的内容，从而获得更相关的搜索结果。

## 安装

要开始使用 XMLSearchTool，必须先安装 crewai\_tools 包。这可以通过以下命令轻松完成：

```shell  theme={null}
pip install 'crewai[tools]'
```

## 示例

以下两个示例演示了如何使用 XMLSearchTool。第一个示例展示了如何在特定 XML 文件中搜索，而第二个示例说明了在未预定义 XML 路径的情况下启动搜索，提供了搜索范围的灵活性。

```python Code theme={null}
from crewai_tools import XMLSearchTool

# 允许代理在任何 XML 文件内容中搜索
# 因为它会在执行过程中了解它们的路径
tool = XMLSearchTool()

# 或者

# 使用特定的 XML 文件路径初始化工具
# 以便仅在该文档中进行搜索
tool = XMLSearchTool(xml='path/to/your/xmlfile.xml')
```

## 参数

* `xml`：这是您希望搜索的 XML 文件的路径。
  它是工具初始化过程中的可选参数，但必须在初始化时或作为 `run` 方法参数的一部分提供，以执行搜索。

## 自定义模型和嵌入

默认情况下，该工具使用 OpenAI 进行嵌入和摘要。要自定义模型，可以使用如下配置字典：

```python Code   theme={null}
from chromadb.config import Settings

tool = XMLSearchTool(
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