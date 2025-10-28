# 网站 RAG 搜索

> `WebsiteSearchTool` 旨在对网站内容执行 RAG（检索增强生成）搜索。

# `WebsiteSearchTool`

<Note>
  WebsiteSearchTool 目前处于实验阶段。我们正在积极努力将此工具整合到我们的产品套件中，并将相应更新文档。
</Note>

## 描述

WebsiteSearchTool 作为一个概念被设计用于在网站内容中进行语义搜索。
它旨在利用像检索增强生成（RAG）这样的先进机器学习模型，高效地导航和提取指定 URL 中的信息。
该工具旨在提供灵活性，允许用户在任何网站上执行搜索，或专注于感兴趣的特定网站。
请注意，WebsiteSearchTool 的当前实现细节正在开发中，所描述的功能可能尚不可用。

## 安装

为了在 WebsiteSearchTool 可用时准备好您的环境，您可以安装基础包：

```shell  theme={null}
pip install 'crewai[tools]'
```

此命令安装必要的依赖项，以确保一旦工具完全集成，用户可以立即开始使用它。

## 使用示例

以下是 WebsiteSearchTool 在不同场景中如何使用的示例。请注意，这些示例是说明性的，代表计划中的功能：

```python Code theme={null}
from crewai_tools import WebsiteSearchTool

# 启动工具的示例，代理可以使用该工具
# 搜索任何发现的网站
tool = WebsiteSearchTool()

# 将搜索限制在特定网站内容的示例，
# 这样代理只能在该网站内搜索
tool = WebsiteSearchTool(website='https://example.com')
```

## 参数

* `website`：一个可选参数，用于指定要进行专注搜索的网站 URL。此参数旨在通过在必要时允许有针对性的搜索来增强工具的灵活性。

## 自定义选项

默认情况下，该工具使用 OpenAI 进行嵌入和摘要。要自定义模型，您可以使用配置字典，如下所示：

```python Code theme={null}
tool = WebsiteSearchTool(
    config=dict(
        llm=dict(
            provider="ollama", # 或 google、openai、anthropic、llama2、...
            config=dict(
                model="llama2",
                # temperature=0.5,
                # top_p=1,
                # stream=true,
            ),
        ),
        embedder=dict(
            provider="google", # 或 openai、ollama、...
            config=dict(
                model="models/embedding-001",
                task_type="retrieval_document",
                # title="Embeddings",
            ),
        ),
    )
)
```