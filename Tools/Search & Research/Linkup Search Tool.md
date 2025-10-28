# Linkup 搜索工具

> `LinkupSearchTool` 能够查询 Linkup API 以获取上下文信息。

# `LinkupSearchTool`

## 描述

`LinkupSearchTool` 提供了查询 Linkup API 以获取上下文信息并检索结构化结果的能力。该工具非常适合使用来自 Linkup 的最新可靠信息来丰富工作流，允许代理在执行任务时访问相关数据。

## 安装

要使用此工具，您需要安装 Linkup SDK：

```shell  theme={null}
uv add linkup-sdk
```

## 入门步骤

要有效使用 `LinkupSearchTool`，请遵循以下步骤：

1.  **API 密钥**：获取 Linkup API 密钥。
2.  **环境设置**：使用 API 密钥设置您的环境。
3.  **安装 SDK**：使用上述命令安装 Linkup SDK。

## 示例

以下示例演示了如何初始化工具并在代理中使用它：

```python Code theme={null}
from crewai_tools import LinkupSearchTool
from crewai import Agent
import os

# 使用您的 API 密钥初始化工具
linkup_tool = LinkupSearchTool(api_key=os.getenv("LINKUP_API_KEY"))

# 定义一个使用该工具的代理
@agent
def researcher(self) -> Agent:
    '''
    该代理使用 LinkupSearchTool 从 Linkup API 检索上下文信息。
    '''
    return Agent(
        config=self.agents_config["researcher"],
        tools=[linkup_tool]
    )
```

## 参数

`LinkupSearchTool` 接受以下参数：

### 构造函数参数

* **api\_key**：必需。您的 Linkup API 密钥。

### 运行参数

* **query**：必需。搜索词或短语。
* **depth**：可选。搜索深度。默认为 "standard"（标准）。
* **output\_type**：可选。输出类型。默认为 "searchResults"（搜索结果）。

## 高级用法

您可以自定义搜索参数以获得更具体的结果：

```python Code theme={null}
# 使用自定义参数执行搜索
results = linkup_tool.run(
    query="Women Nobel Prize Physics",
    depth="deep",
    output_type="searchResults"
)
```

## 返回格式

该工具返回以下格式的结果：

```json  theme={null}
{
  "success": true,
  "results": [
    {
      "name": "结果标题",
      "url": "https://example.com/result",
      "content": "结果的内容..."
    },
    // 更多结果...
  ]
}
```

如果发生错误，响应将是：

```json  theme={null}
{
  "success": false,
  "error": "错误信息"
}
```

## 错误处理

该工具能够优雅地处理 API 错误并提供结构化反馈。如果 API 请求失败，工具将返回一个包含 `success: false` 和错误信息的字典。

## 结论

`LinkupSearchTool` 提供了一种将 Linkup 的上下文信息检索能力无缝集成到您的 CrewAI 代理中的方法。通过利用此工具，代理可以访问相关且最新的信息，以增强其决策和任务执行能力。