# JSON RAG 搜索

> `JSONSearchTool` 旨在搜索 JSON 文件并返回最相关的结果。

# `JSONSearchTool`

<Note>
  JSONSearchTool 目前处于实验阶段。这意味着该工具正在积极开发中，用户可能会遇到意外行为或变更。我们强烈鼓励用户就任何问题或改进建议提供反馈。
</Note>

## 描述

JSONSearchTool 旨在促进 JSON 文件内容内的高效精确搜索。它利用 RAG（检索与生成）搜索机制，允许用户指定 JSON 路径以在特定 JSON 文件中进行目标搜索。此功能显著提高了搜索结果的准确性和相关性。

## 安装

要安装 JSONSearchTool，请使用以下 pip 命令：

```shell  theme={null}
pip install 'crewai[tools]'
```

## 使用示例

以下是有关如何有效利用 JSONSearchTool 搜索 JSON 文件的更新示例。这些示例考虑了代码库中当前确定的实现和使用模式。

```python Code theme={null}
from crewai_tools import JSONSearchTool

# 通用 JSON 内容搜索
# 当 JSON 路径预先已知或可以动态识别时，此方法适用。
tool = JSONSearchTool()

# 将搜索限制在特定 JSON 文件
# 当您希望将搜索范围限制在特定 JSON 文件时，请使用此初始化方法。
tool = JSONSearchTool(json_path='./path/to/your/file.json')
```

## 参数

* `json_path` (str, 可选)：指定要搜索的 JSON 文件的路径。如果工具是为通用搜索初始化的，则此参数不是必需的。提供时，它会将搜索限制在指定的 JSON 文件内。

## 配置选项

JSONSearchTool 支持通过配置字典进行广泛的自定义。这允许用户根据其需求选择不同的模型进行嵌入和摘要。

```python Code theme={null}
tool = JSONSearchTool(
    config={
        "llm": {
            "provider": "ollama",  # 其他选项包括 google, openai, anthropic, llama2 等。
            "config": {
                "model": "llama2",
                # 可以在此处指定其他可选配置。
                # temperature=0.5,
                # top_p=1,
                # stream=true,
            },
        },
        "embedding_model": {
            "provider": "google", # 或 openai, ollama, ...
            "config": {
                "model": "models/embedding-001",
                "task_type": "retrieval_document",
                # 可以在此处添加进一步的自定义选项。
            },
        },
    }
)
```