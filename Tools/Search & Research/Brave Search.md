# Brave 搜索

> `BraveSearchTool` 旨在使用 Brave 搜索 API 进行互联网搜索。

# `BraveSearchTool`

## 描述

该工具设计用于使用 Brave 搜索 API 执行网络搜索。它允许您使用指定查询搜索互联网并检索相关结果。该工具支持自定义结果数量和国家特定搜索。

## 安装

要将此工具集成到您的项目中，请按照以下安装说明操作：

```shell  theme={null}
pip install 'crewai[tools]'
```

## 入门步骤

要有效使用 `BraveSearchTool`，请按照以下步骤操作：

1. **包安装**：确认 `crewai[tools]` 包已安装在您的 Python 环境中。
2. **API 密钥获取**：在 [https://api.search.brave.com/app/keys](https://api.search.brave.com/app/keys) 获取 Brave 搜索 API 密钥（登录以生成密钥）。
3. **环境配置**：将您获取的 API 密钥存储在名为 `BRAVE_API_KEY` 的环境变量中，以便工具使用。

## 示例

以下示例演示了如何初始化工具并使用给定查询执行搜索：

```python Code theme={null}
from crewai_tools import BraveSearchTool

# 初始化工具以实现互联网搜索功能
tool = BraveSearchTool()

# 执行搜索
results = tool.run(search_query="CrewAI agent framework")
print(results)
```

## 参数

`BraveSearchTool` 接受以下参数：

* **search\_query**：必需。您要用于搜索互联网的搜索查询。
* **country**：可选。指定搜索结果的国家。默认为空字符串。
* **n\_results**：可选。要返回的搜索结果数量。默认为 `10`。
* **save\_file**：可选。是否将搜索结果保存到文件。默认为 `False`。

## 带参数的示例

以下是演示如何使用带有附加参数的工具的示例：

```python Code theme={null}
from crewai_tools import BraveSearchTool

# 使用自定义参数初始化工具
tool = BraveSearchTool(
    country="US",
    n_results=5,
    save_file=True
)

# 执行搜索
results = tool.run(search_query="Latest AI developments")
print(results)
```

## 代理集成示例

以下是如何将 `BraveSearchTool` 与 CrewAI 代理集成：

```python Code theme={null}
from crewai import Agent
from crewai.project import agent
from crewai_tools import BraveSearchTool

# 初始化工具
brave_search_tool = BraveSearchTool()

# 使用 BraveSearchTool 定义代理
@agent
def researcher(self) -> Agent:
    return Agent(
        config=self.agents_config["researcher"],
        allow_delegation=False,
        tools=[brave_search_tool]
    )
```

## 结论

通过将 `BraveSearchTool` 集成到 Python 项目中，用户能够直接从其应用程序在互联网上进行实时、相关的搜索。该工具为强大的 Brave 搜索 API 提供了简单接口，使以编程方式检索和处理搜索结果变得容易。通过遵循提供的设置和使用指南，将该工具集成到项目中变得简化而直接。