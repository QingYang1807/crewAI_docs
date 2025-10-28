# Weaviate 向量搜索

> `WeaviateVectorSearchTool` 旨在使用混合搜索在 Weaviate 向量数据库中搜索语义相似的文档。

## 概述

`WeaviateVectorSearchTool` 是专为在 Weaviate 向量数据库中存储的文档进行语义搜索而设计的。该工具允许您查找与给定查询语义相似的文档，利用向量和关键词搜索的力量，以获得更准确、更符合上下文的搜索结果。

[Weaviate](https://weaviate.io/) 是一个向量数据库，用于存储和查询向量嵌入，从而实现语义搜索功能。

## 安装

要将此工具集成到您的项目中，您需要安装 Weaviate 客户端：

```shell  theme={null}
uv add weaviate-client
```

## 入门步骤

要有效使用 `WeaviateVectorSearchTool`，请遵循以下步骤：

1.  **包安装**：确认您的 Python 环境中已安装 `crewai[tools]` 和 `weaviate-client` 包。
2.  **Weaviate 设置**：设置一个 Weaviate 集群。您可以遵循 [Weaviate 文档](https://weaviate.io/developers/wcs/manage-clusters/connect) 中的说明进行操作。
3.  **API 密钥**：获取您的 Weaviate 集群 URL 和 API 密钥。
4.  **OpenAI API 密钥**：确保您已在环境变量中将 OpenAI API 密钥设置为 `OPENAI_API_KEY`。

## 示例

以下示例演示了如何初始化工具并执行搜索：

```python Code theme={null}
from crewai_tools import WeaviateVectorSearchTool

# 初始化工具
tool = WeaviateVectorSearchTool(
    collection_name='example_collections',
    limit=3,
    alpha=0.75,
    weaviate_cluster_url="https://your-weaviate-cluster-url.com",
    weaviate_api_key="your-weaviate-api-key",
)

@agent
def search_agent(self) -> Agent:
    '''
    该代理使用 WeaviateVectorSearchTool 在 Weaviate 向量数据库中搜索
    语义相似的文档。
    '''
    return Agent(
        config=self.agents_config["search_agent"],
        tools=[tool]
    )
```

## 参数

`WeaviateVectorSearchTool` 接受以下参数：

* **collection\_name**：必需。要在其中搜索的集合的名称。
* **weaviate\_cluster\_url**：必需。Weaviate 集群的 URL。
* **weaviate\_api\_key**：必需。Weaviate 集群的 API 密钥。
* **limit**：可选。要返回的结果数量。默认为 `3`。
* **alpha**：可选。控制向量和关键词 (BM25) 搜索之间的权重。alpha = 0 -> 仅 BM25，alpha = 1 -> 仅向量搜索。默认为 `0.75`。
* **vectorizer**：可选。要使用的向量化器。如果未提供，它将使用 `text2vec_openai` 和 `nomic-embed-text` 模型。
* **generative\_model**：可选。要使用的生成式模型。如果未提供，它将使用 OpenAI 的 `gpt-4o`。

## 高级配置

您可以自定义工具使用的向量化器和生成式模型：

```python Code theme={null}
from crewai_tools import WeaviateVectorSearchTool
from weaviate.classes.config import Configure

# 为向量化器和生成式模型设置自定义模型
tool = WeaviateVectorSearchTool(
    collection_name='example_collections',
    limit=3,
    alpha=0.75,
    vectorizer=Configure.Vectorizer.text2vec_openai(model="nomic-embed-text"),
    generative_model=Configure.Generative.openai(model="gpt-4o-mini"),
    weaviate_cluster_url="https://your-weaviate-cluster-url.com",
    weaviate_api_key="your-weaviate-api-key",
)
```

## 预加载文档

您可以在使用工具之前，将文档预加载到您的 Weaviate 数据库中：

```python Code theme={null}
import os
from crewai_tools import WeaviateVectorSearchTool
import weaviate
from weaviate.classes.init import Auth

# 连接到 Weaviate
client = weaviate.connect_to_weaviate_cloud(
    cluster_url="https://your-weaviate-cluster-url.com",
    auth_credentials=Auth.api_key("your-weaviate-api-key"),
    headers={"X-OpenAI-Api-Key": "your-openai-api-key"}
)

# 获取或创建集合
test_docs = client.collections.get("example_collections")
if not test_docs:
    test_docs = client.collections.create(
        name="example_collections",
        vectorizer_config=Configure.Vectorizer.text2vec_openai(model="nomic-embed-text"),
        generative_config=Configure.Generative.openai(model="gpt-4o"),
    )

# 加载文档
docs_to_load = os.listdir("knowledge")
with test_docs.batch.dynamic() as batch:
    for d in docs_to_load:
        with open(os.path.join("knowledge", d), "r") as f:
            content = f.read()
        batch.add_object(
            {
                "content": content,
                "year": d.split("_")[0],
            }
        )

# 初始化工具
tool = WeaviateVectorSearchTool(
    collection_name='example_collections', 
    limit=3,
    alpha=0.75,
    weaviate_cluster_url="https://your-weaviate-cluster-url.com",
    weaviate_api_key="your-weaviate-api-key",
)
```

## 代理集成示例

以下是如何将 `WeaviateVectorSearchTool` 与 CrewAI 代理集成：

```python Code theme={null}
from crewai import Agent
from crewai_tools import WeaviateVectorSearchTool

# 初始化工具
weaviate_tool = WeaviateVectorSearchTool(
    collection_name='example_collections',
    limit=3,
    alpha=0.75,
    weaviate_cluster_url="https://your-weaviate-cluster-url.com",
    weaviate_api_key="your-weaviate-api-key",
)

# 创建一个带有该工具的代理
rag_agent = Agent(
    name="rag_agent",
    role="You are a helpful assistant that can answer questions with the help of the WeaviateVectorSearchTool.",
    llm="gpt-4o-mini",
    tools=[weaviate_tool],
)
```

## 结论

`WeaviateVectorSearchTool` 提供了一种强大的方式，用于在 Weaviate 向量数据库中搜索语义相似的文档。通过利用向量嵌入，与传统的基于关键词的搜索相比，它能够实现更准确、更符合上下文的搜索结果。该工具对于需要根据含义而非精确匹配来查找信息的应用程序特别有用。