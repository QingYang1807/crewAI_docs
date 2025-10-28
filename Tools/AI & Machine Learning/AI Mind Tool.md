# AI 心智工具

> `AIMindTool` 旨在以自然语言查询数据源。

# `AIMindTool`

## 描述

`AIMindTool` 是对 [MindsDB](https://mindsdb.com/) 提供的 [AI-Minds](https://mindsdb.com/minds) 的封装。它允许您只需配置连接参数，就可以用自然语言查询数据源。当您需要从存储在各种数据源（包括 PostgreSQL、MySQL、MariaDB、ClickHouse、Snowflake 和 Google BigQuery）中的数据获取问题答案时，此工具非常有用。

心智是类似于大型语言模型（LLM）的 AI 系统，但它更进一步，可以从任何数据中回答任何问题。这是通过以下方式实现的：

* 使用参数化搜索选择与答案最相关的数据
* 通过语义搜索理解含义并在正确的上下文中提供响应
* 通过分析数据和使用机器学习（ML）模型提供精确的答案

## 安装

要将此工具集成到您的项目中，您需要安装 Minds SDK：

```shell  theme={null}
uv add minds-sdk
```

## 入门步骤

要有效使用 `AIMindTool`，请按照以下步骤操作：

1. **包安装**：确认在您的 Python 环境中已安装 `crewai[tools]` 和 `minds-sdk` 包。
2. **API 密钥获取**：在[此处](https://mdb.ai/register)注册 Minds 账户，并获取 API 密钥。
3. **环境配置**：将您获得的 API 密钥存储在名为 `MINDS_API_KEY` 的环境变量中，以便工具使用。

## 示例

以下示例演示了如何初始化工具并执行查询：

```python Code theme={null}
from crewai_tools import AIMindTool

# 初始化 AIMindTool
aimind_tool = AIMindTool(
    datasources=[
        {
            "description": "房屋销售数据",
            "engine": "postgres",
            "connection_data": {
                "user": "demo_user",
                "password": "demo_password",
                "host": "samples.mindsdb.com",
                "port": 5432,
                "database": "demo",
                "schema": "demo_data"
            },
            "tables": ["house_sales"]
        }
    ]
)

# 运行自然语言查询
result = aimind_tool.run("2008 年售出了多少套三居室房屋？")
print(result)
```

## 参数

`AIMindTool` 接受以下参数：

* **api\_key**：可选。您的 Minds API 密钥。如果未提供，将从 `MINDS_API_KEY` 环境变量中读取。
* **datasources**：字典列表，每个字典包含以下键：
  * **description**：对数据源中包含的数据的描述。
  * **engine**：数据源的引擎（或类型）。
  * **connection_data**：包含数据源连接参数的字典。
  * **tables**：数据源将使用的表列表。这是可选的，如果要使用数据源中的所有表，可以省略。

支持的数据源及其连接参数列表可在[此处](https://docs.mdb.ai/docs/data_sources)找到。

## 代理集成示例

以下是如何将 `AIMindTool` 与 CrewAI 代理集成：

```python Code theme={null}
from crewai import Agent
from crewai.project import agent
from crewai_tools import AIMindTool

# 初始化工具
aimind_tool = AIMindTool(
    datasources=[
        {
            "description": "销售数据",
            "engine": "postgres",
            "connection_data": {
                "user": "your_user",
                "password": "your_password",
                "host": "your_host",
                "port": 5432,
                "database": "your_db",
                "schema": "your_schema"
            },
            "tables": ["sales"]
        }
    ]
)

# 定义带有 AIMindTool 的代理
@agent
def data_analyst(self) -> Agent:
    return Agent(
        config=self.agents_config["data_analyst"],
        allow_delegation=False,
        tools=[aimind_tool]
    )
```

## 结论

`AIMindTool` 提供了一种强大的方式来使用自然语言查询您的数据源，使得无需编写复杂的 SQL 查询即可提取洞察。通过连接到各种数据源并利用 AI-Minds 技术，该工具使代理能够高效地访问和分析数据。