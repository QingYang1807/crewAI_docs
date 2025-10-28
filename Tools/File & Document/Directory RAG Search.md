# 目录 RAG 搜索

> `DirectorySearchTool` 是一个强大的 RAG（检索增强生成）工具，专为在目录内容中进行语义搜索而设计。

# `DirectorySearchTool`

<Note>
  **实验性功能**：DirectorySearchTool 正在持续开发中。功能和特性可能会不断演进，在完善工具的过程中可能会出现意外行为。
</Note>

## 描述

DirectorySearchTool 能够在指定目录的内容中进行语义搜索，利用检索增强生成（RAG）方法高效地浏览文件。该工具设计灵活，允许用户在运行时动态指定搜索目录，或在初始设置时设定固定目录。

## 安装

要使用 DirectorySearchTool，首先需要安装 crewai_tools 包。在终端中执行以下命令：

```shell  theme={null}
pip install 'crewai[tools]'
```

## 初始化与使用

从 `crewai_tools` 包中导入 DirectorySearchTool 以开始使用。您可以在不指定目录的情况下初始化工具，从而在运行时设置搜索目录。或者，也可以使用预定义目录来初始化工具。

```python Code theme={null}
from crewai_tools import DirectorySearchTool

# 用于在运行时动态指定目录
tool = DirectorySearchTool()

# 用于固定目录搜索
tool = DirectorySearchTool(directory='/path/to/directory')
```

## 参数

* `directory`：一个字符串参数，用于指定搜索目录。在初始化时这是可选的，但如果初始未设置，则在搜索时是必需的。

## 自定义模型和嵌入

DirectorySearchTool 默认使用 OpenAI 进行嵌入和摘要处理。这些设置的自定义选项包括更改模型提供者和配置，为高级用户增强灵活性。

```python Code theme={null}
from chromadb.config import Settings

tool = DirectorySearchTool(
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