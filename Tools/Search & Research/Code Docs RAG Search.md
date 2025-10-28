# 代码文档 RAG 搜索

> `CodeDocsSearchTool` 是一个强大的 RAG（检索增强生成）工具，专为在代码文档中进行语义搜索而设计。

# `CodeDocsSearchTool`

<Note>
  **实验性**：我们仍在努力改进工具，因此未来可能会有意外行为或变更。
</Note>

## 描述

CodeDocsSearchTool 是一个强大的 RAG（检索增强生成）工具，专为在代码文档中进行语义搜索而设计。
它使用户能够有效地在代码文档中查找特定信息或主题。通过在初始化时提供 `docs_url`，
该工具会将搜索范围限定在该特定的文档站点。或者，如果没有指定 `docs_url`，
它会在其执行过程中已知或发现的大量代码文档中进行搜索，使其能够满足各种文档搜索需求，用途广泛。

## 安装

要开始使用 CodeDocsSearchTool，首先通过 pip 安装 crewai_tools 包：

```shell  theme={null}
pip install 'crewai[tools]'
```

## 示例

如下所示使用 CodeDocsSearchTool 在代码文档中进行搜索：

```python Code theme={null}
from crewai_tools import CodeDocsSearchTool

# 要搜索任何代码文档内容
# 如果 URL 在执行过程中已知或被发现：
tool = CodeDocsSearchTool()

# 或者

# 通过提供 URL 来专门将搜索集中在给定的文档站点：
tool = CodeDocsSearchTool(docs_url='https://docs.example.com/reference')
```

<Note>
  将 '[https://docs.example.com/reference](https://docs.example.com/reference)' 替换为目标文档 URL，
  并将 'How to use search tool' 替换为与您的需求相关的搜索查询。
</Note>

## 参数

以下参数可用于自定义 `CodeDocsSearchTool` 的行为：

| 参数         | 类型     | 描述                                                                 |
| :----------- | :------- | :------------------------------------------------------------------ |
| **docs_url** | `string` | *可选*。指定要搜索的代码文档的 URL。                                |

## 自定义模型和嵌入

默认情况下，该工具使用 OpenAI 进行嵌入和摘要。要自定义模型，您可以使用如下配置字典：

```python Code theme={null}
tool = CodeDocsSearchTool(
    config=dict(
        llm=dict(
            provider="ollama", # 或 google, openai, anthropic, llama2, ...
            config=dict(
                model="llama2",
                # temperature=0.5,
                # top_p=1,
                # stream=true,
            ),
        ),
        embedder=dict(
            provider="google", # 或 openai, ollama, ...
            config=dict(
                model="models/embedding-001",
                task_type="retrieval_document",
                # title="Embeddings",
            ),
        ),
    )
)
```