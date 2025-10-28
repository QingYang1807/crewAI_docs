# MDX RAG 搜索

> `MDXSearchTool` 旨在搜索 MDX 文件并返回最相关的结果。

# `MDXSearchTool`

<Note>
  MDXSearchTool 正在持续开发中。随着我们不断完善该工具，可能会添加或删除功能，其使用方式也可能发生不可预测的变化。
</Note>

## 描述

MDX 搜索工具是 `crewai_tools` 包的一个组件，旨在促进高级 markdown 语言提取。它使用户能够通过基于查询的搜索有效地搜索和提取 MD 文件中的相关信息。该工具对于数据分析、信息管理和研究任务非常有价值，能够简化在大型文档集合中查找特定信息的过程。

## 安装

在使用 MDX 搜索工具之前，请确保已安装 `crewai_tools` 包。如果尚未安装，可以使用以下命令进行安装：

```shell theme={null}
pip install 'crewai[tools]'
```

## 使用示例

要使用 MDX 搜索工具，首先必须设置必要的环境变量。然后，将该工具集成到您的 crewAI 项目中以开始市场研究。下面是一个基本的使用示例：

```python Code theme={null}
from crewai_tools import MDXSearchTool

# 初始化工具以搜索执行过程中了解到的任何 MDX 内容
tool = MDXSearchTool()

# 或者

# 使用特定的 MDX 文件路径初始化工具，以在该文档内进行专门搜索
tool = MDXSearchTool(mdx='path/to/your/document.mdx')
```

## 参数

* mdx：**可选**。指定搜索的 MDX 文件路径。可以在初始化时提供。

## 模型和嵌入的自定义

该工具默认使用 OpenAI 进行嵌入和摘要。如需自定义，请使用如下所示的配置字典：

```python Code theme={null}
from chromadb.config import Settings

tool = MDXSearchTool(
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