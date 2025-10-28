# Qdrant 向量搜索工具

> 为 CrewAI 代理提供基于 Qdrant 向量数据库的语义搜索功能

## 概述

Qdrant 向量搜索工具通过利用 [Qdrant](https://qdrant.tech/)（一种向量相似性搜索引擎），为您的 CrewAI 代理提供语义搜索功能。该工具允许您的代理使用语义相似性在 Qdrant 集合中搜索存储的文档。

## 安装

安装所需软件包：

```bash  theme={null}
uv add qdrant-client
```

## 基本用法

以下是如何使用该工具的最小示例：

```python  theme={null}
from crewai import Agent
from crewai_tools import QdrantVectorSearchTool

# 初始化工具
qdrant_tool = QdrantVectorSearchTool(
    qdrant_url="your_qdrant_url",
    qdrant_api_key="your_qdrant_api_key",
    collection_name="your_collection"
)

# 创建使用该工具的代理
agent = Agent(
    role="研究助理",
    goal="在文档中查找相关信息",
    tools=[qdrant_tool]
)

# 该工具将自动使用 OpenAI 嵌入
# 并返回相似度得分大于 0.35 的 3 个最相关结果
```

## 完整工作示例

以下是一个完整的示例，展示了如何：

1. 从 PDF 中提取文本
2. 使用 OpenAI 生成嵌入
3. 存储到 Qdrant
4. 创建 CrewAI 代理式 RAG 工作流以进行语义搜索

```python  theme={null}
import os
import uuid
import pdfplumber
from openai import OpenAI
from dotenv import load_dotenv
from crewai import Agent, Task, Crew, Process, LLM
from crewai_tools import QdrantVectorSearchTool
from qdrant_client import QdrantClient
from qdrant_client.models import PointStruct, Distance, VectorParams

# 加载环境变量
load_dotenv()

# 初始化 OpenAI 客户端
client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

# 从 PDF 中提取文本
def extract_text_from_pdf(pdf_path):
    text = []
    with pdfplumber.open(pdf_path) as pdf:
        for page in pdf.pages:
            page_text = page.extract_text()
            if page_text:
                text.append(page_text.strip())
    return text

# 生成 OpenAI 嵌入
def get_openai_embedding(text):
    response = client.embeddings.create(
        input=text,
        model="text-embedding-3-small"
    )
    return response.data[0].embedding

# 将文本和嵌入存储到 Qdrant
def load_pdf_to_qdrant(pdf_path, qdrant, collection_name):
    # 从 PDF 中提取文本
    text_chunks = extract_text_from_pdf(pdf_path)
  
    # 创建 Qdrant 集合
    if qdrant.collection_exists(collection_name):
        qdrant.delete_collection(collection_name)
    qdrant.create_collection(
        collection_name=collection_name,
        vectors_config=VectorParams(size=1536, distance=Distance.COSINE)
    )

    # 存储嵌入
    points = []
    for chunk in text_chunks:
        embedding = get_openai_embedding(chunk)
        points.append(PointStruct(
            id=str(uuid.uuid4()),
            vector=embedding,
            payload={"text": chunk}
        ))
    qdrant.upsert(collection_name=collection_name, points=points)

# 初始化 Qdrant 客户端并加载数据
qdrant = QdrantClient(
    url=os.getenv("QDRANT_URL"),
    api_key=os.getenv("QDRANT_API_KEY")
)
collection_name = "example_collection"
pdf_path = "path/to/your/document.pdf"
load_pdf_to_qdrant(pdf_path, qdrant, collection_name)

# 初始化 Qdrant 搜索工具
qdrant_tool = QdrantVectorSearchTool(
    qdrant_url=os.getenv("QDRANT_URL"),
    qdrant_api_key=os.getenv("QDRANT_API_KEY"),
    collection_name=collection_name,
    limit=3,
    score_threshold=0.35
)

# 创建 CrewAI 代理
search_agent = Agent(
    role="高级语义搜索代理",
    goal="基于语义搜索查找并分析文档",
    backstory="""您是一位专业的研究助理，能够使用 Qdrant 数据库中的语义搜索
    查找相关信息。""",
    tools=[qdrant_tool],
    verbose=True
)

answer_agent = Agent(
    role="高级回答助理",
    goal="根据提供的上下文生成问题的答案",
    backstory="""您是一位专业的回答助理，能够根据提供的上下文
    生成问题的答案。""",
    tools=[qdrant_tool],
    verbose=True
)

# 定义任务
search_task = Task(
    description="""搜索关于 {query} 的相关文档。
    您的最终答案应包括：
    - 找到的相关信息
    - 结果的相似度得分
    - 相关文档的元数据""",
    agent=search_agent
)

answer_task = Task(
    description="""根据相关文档的上下文和元数据，
    基于上下文生成最终答案。""",
    agent=answer_agent
)

# 运行 CrewAI 工作流
crew = Crew(
    agents=[search_agent, answer_agent],
    tasks=[search_task, answer_task],
    process=Process.sequential,
    verbose=True
)

result = crew.kickoff(
    inputs={"query": "文档中 X 的角色是什么？"}
)
print(result)
```

## 工具参数

### 必需参数

* `qdrant_url` (str): 您的 Qdrant 服务器 URL
* `qdrant_api_key` (str): 用于 Qdrant 身份验证的 API 密钥
* `collection_name` (str): 要搜索的 Qdrant 集合名称

### 可选参数

* `limit` (int): 返回的最大结果数（默认：3）
* `score_threshold` (float): 最小相似度得分阈值（默认：0.35）
* `custom_embedding_fn` (Callable\[\[str], list\[float]]): 用于文本向量化的自定义函数

## 搜索参数

该工具在其模式中接受以下参数：

* `query` (str): 用于查找相似文档的搜索查询
* `filter_by` (str, 可选): 要过滤的元数据字段
* `filter_value` (str, 可选): 要过滤的值

## 返回格式

该工具以 JSON 格式返回结果：

```json  theme={null}
[
  {
    "metadata": {
      // 与文档一起存储的任何元数据
    },
    "context": "文档的实际文本内容",
    "distance": 0.95  // 相似度得分
  }
]
```

## 默认嵌入

默认情况下，该工具使用 OpenAI 的 `text-embedding-3-small` 模型进行向量化。这需要：

* 在环境中设置的 OpenAI API 密钥：`OPENAI_API_KEY`

## 自定义嵌入

除了使用默认的嵌入模型，您可能希望使用自己的嵌入函数，例如当您：

1. 想要使用不同的嵌入模型（如 Cohere、HuggingFace、Ollama 模型）
2. 需要通过使用开源嵌入模型来降低成本
3. 对向量维度或嵌入质量有特定要求
4. 想要使用特定领域的嵌入（例如医学或法律文本）

以下是使用 HuggingFace 模型的示例：

```python  theme={null}
from transformers import AutoTokenizer, AutoModel
import torch

# 加载模型和分词器
tokenizer = AutoTokenizer.from_pretrained('sentence-transformers/all-MiniLM-L6-v2')
model = AutoModel.from_preputed('sentence-transformers/all-MiniLM-L6-v2')

def custom_embeddings(text: str) -> list[float]:
    # 分词并获取模型输出
    inputs = tokenizer(text, return_tensors="pt", padding=True, truncation=True)
    outputs = model(**inputs)
  
    # 使用平均池化获取文本嵌入
    embeddings = outputs.last_hidden_state.mean(dim=1)
  
    # 转换为浮点数列表并返回
    return embeddings[0].tolist()

# 使用自定义嵌入的工具
tool = QdrantVectorSearchTool(
    qdrant_url="your_url",
    qdrant_api_key="your_key",
    collection_name="your_collection",
    custom_embedding_fn=custom_embeddings  # 传递您的自定义函数
)
```

## 错误处理

该工具处理以下特定错误：

* 如果未安装 `qdrant-client`，则引发 ImportError（可选择自动安装）
* 如果未设置 `QDRANT_URL`，则引发 ValueError
* 如果缺少 `qdrant-client`，提示使用 `uv add qdrant-client` 安装

## 环境变量

必需的环境变量：

```bash  theme={null}
export QDRANT_URL="your_qdrant_url"  # 如果未在构造函数中提供
export QDRANT_API_KEY="your_api_key"  # 如果未在构造函数中提供
export OPENAI_API_KEY="your_openai_key"  # 如果使用默认嵌入
```