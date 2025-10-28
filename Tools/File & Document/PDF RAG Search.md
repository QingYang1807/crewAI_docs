# PDF RAG 搜索

> `PDFSearchTool` 旨在搜索 PDF 文件并返回最相关的结果。

# `PDFSearchTool`

<Note>
  我们仍在努力改进工具，因此将来可能会有意外行为或变更。
</Note>

## 描述

PDFSearchTool 是一个 RAG 工具，专为 PDF 内容内的语义搜索而设计。它允许输入搜索查询和 PDF 文档，利用先进的搜索技术高效地查找相关内容。
这一能力使其特别适用于从大型 PDF 文件中快速提取特定信息。

## 安装

要开始使用 PDFSearchTool，首先确保通过以下命令安装 crewai_tools 包：

```shell  theme={null}
pip install 'crewai[tools]'
```

## 示例

以下是使用 PDFSearchTool 在 PDF 文档中搜索的方法：

```python Code theme={null}
from crewai_tools import PDFSearchTool

# 初始化工具，如果在执行期间提供路径，则允许搜索任何 PDF 内容
tool = PDFSearchTool()

# 或者

# 使用特定 PDF 路径初始化工具，以仅在该文档内进行搜索
tool = PDFSearchTool(pdf='path/to/your/document.pdf')
```

## 参数

* `pdf`：**可选** 搜索的 PDF 路径。可以在初始化时或在 `run` 方法的参数中提供。如果在初始化时提供，工具将其搜索范围限制在指定文档内。

## 自定义模型和嵌入

默认情况下，该工具使用 OpenAI 进行嵌入和摘要。要自定义模型，可以使用如下配置字典。注意：需要向量数据库，因为生成的嵌入必须存储在向量数据库中并从中查询。

```python Code theme={null}
from crewai_tools import PDFSearchTool

# - embedding_model (必需)：选择提供者 + 提供者特定的配置
# - vectordb (必需)：选择向量数据库并传递其配置

tool = PDFSearchTool(
    config={
        "embedding_model": {
            # 支持的提供者："openai", "azure", "google-generativeai", "google-vertex",
            # "voyageai", "cohere", "huggingface", "jina", "sentence-transformer",
            # "text2vec", "ollama", "openclip", "instructor", "onnx", "roboflow", "watsonx", "custom"
            "provider": "openai",  # 或："google-generativeai", "cohere", "ollama", ...
            "config": {
                # 所选提供者的模型标识符。"model" 将在内部自动映射为 "model_name"。
                "model": "text-embedding-3-small",
                # 可选：API 密钥。如果省略，工具将在可用时使用提供者特定的环境变量
                # (例如，provider="openai" 时使用 OPENAI_API_KEY)。
                # "api_key": "sk-...",

                # 提供者特定示例：
                # --- Google Generative AI ---
                # (在上面设置 provider="google-generativeai")
                # "model": "models/embedding-001",
                # "task_type": "retrieval_document",
                # "title": "Embeddings",

                # --- Cohere ---
                # (在上面设置 provider="cohere")
                # "model": "embed-english-v3.0",

                # --- Ollama (本地) ---
                # (在上面设置 provider="ollama")
                # "model": "nomic-embed-text",
            },
        },
        "vectordb": {
                    "provider": "chromadb",  # 或 "qdrant"
                    "config": {
                        # 对于 ChromaDB：传递 "settings" (chromadb.config.Settings) 或依赖默认值。
                        # 示例 (取消注释并导入)：
                        # from chromadb.config import Settings
                        # "settings": Settings(
                        #     persist_directory="/content/chroma",
                        #     allow_reset=True,
                        #     is_persistent=True,
                        # ),

                        # 对于 Qdrant：传递 "vectors_config" (qdrant_client.models.VectorParams)。
                        # 示例 (取消注释并导入)：
                        # from qdrant_client.models import VectorParams, Distance
                        # "vectors_config": VectorParams(size=384, distance=Distance.COSINE),

                        # 注意：集合名称由工具控制 (默认："rag_tool_collection")，不在此处设置。
                    }
        },
    }
)
```