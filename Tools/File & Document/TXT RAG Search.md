# TXT RAG 搜索

> `TXTSearchTool` 旨在文本文件内容中执行 RAG（检索增强生成）搜索。

## 概述

<Note>
  我们仍在努力改进工具，未来可能会有意外的行为或变更。
</Note>

此工具用于在文本文件的内容中执行 RAG（检索增强生成）搜索。
它允许在指定文本文件的内容中对查询进行语义搜索，
使其成为基于提供的查询快速提取信息或查找特定文本部分的宝贵资源。

## 安装

要使用 `TXTSearchTool`，首先需要安装 `crewai_tools` 包。
可以使用 pip（Python 的包管理器）来完成。
打开终端或命令提示符，输入以下命令：

```shell theme={null}
pip install 'crewai[tools]'
```

此命令将下载并安装 TXTSearchTool 及其所有必要的依赖项。

## 示例

以下示例演示了如何使用 TXTSearchTool 在文本文件中搜索。
此示例展示了使用特定文本文件初始化工具以及随后在该文件内容中进行搜索的过程。

```python Code theme={null}
from crewai_tools import TXTSearchTool

# 初始化工具以搜索代理在执行过程中了解到的任何文本文件内容
tool = TXTSearchTool()

# 或者

# 使用特定文本文件初始化工具，
# 以便代理可以在给定文本文件的内容中进行搜索
tool = TXTSearchTool(txt='path/to/text/file.txt')
```

## 参数

* `txt` (str)：**可选**。您想要搜索的文本文件的路径。
  仅当工具未使用特定文本文件初始化时才需要此参数；
  否则，搜索将在最初提供的文本文件中进行。

## 自定义模型和嵌入

默认情况下，该工具使用 OpenAI 进行嵌入和摘要。
要自定义模型，您可以使用如下配置字典：

```python Code theme={null}
from chromadb.config import Settings

tool = TXTSearchTool(
    config={
        # 必需：嵌入提供程序 + 配置
        "embedding_model": {
            "provider": "openai",  # 或 google-generativeai, cohere, ollama, ...
            "config": {
                "model": "text-embedding-3-small",
                # "api_key": "sk-...",  # 如果设置了环境变量则为可选
                # 提供程序示例：
                # Google → model: "models/embedding-001", task_type: "retrieval_document"
                # Cohere → model: "embed-english-v3.0"
                # Ollama → model: "nomic-embed-text"
            },
        },

        # 必需：向量数据库配置
        "vectordb": {
            "provider": "chromadb",  # 或 "qdrant"
            "config": {
                # Chroma 设置（可选持久性）
                # "settings": Settings(
                #     persist_directory="/content/chroma",
                #     allow_reset=True,
                #     is_persistent=True,
                # ),

                # Qdrant 向量参数示例：
                # from qdrant_client.models import VectorParams, Distance
                # "vectors_config": VectorParams(size=384, distance=Distance.COSINE),

                # 注意：集合名称由工具控制（默认："rag_tool_collection"）。
            }
        },
    }
)
```