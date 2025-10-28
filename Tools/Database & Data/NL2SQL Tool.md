# NL2SQL 工具

> `NL2SQLTool` 旨在将自然语言转换为 SQL 查询。

## 概述

此工具用于将自然语言转换为 SQL 查询。当传递给代理时，它将生成查询，然后使用这些查询与数据库进行交互。

这支持多种工作流程，例如让代理根据目标访问数据库获取信息，然后使用这些信息生成响应、报告或任何其他输出。同时，它还使代理能够根据其目标更新数据库。

**注意**：确保代理有权访问只读副本，或者代理可以在数据库上运行插入/更新查询。

## 要求

* SqlAlchemy
* 任何兼容的数据库库（例如 psycopg2、mysql-connector-python）

## 安装

安装 crewai_tools 包

```shell  theme={null}
pip install 'crewai[tools]'
```

## 使用方法

要使用 NL2SQLTool，您需要将数据库 URI 传递给该工具。URI 应采用 `dialect+driver://username:password@host:port/database` 格式。

```python Code theme={null}
from crewai_tools import NL2SQLTool

# 安装了 psycopg2 来运行此 PostgreSQL 示例
nl2sql = NL2SQLTool(db_uri="postgresql://example@localhost:5432/test_db")

@agent
def researcher(self) -> Agent:
    return Agent(
        config=self.agents_config["researcher"],
        allow_delegation=False,
        tools=[nl2sql]
    )
```

## 示例

主要任务目标是：

"检索每个城市的平均、最高和最低月收入，但只包含拥有多个用户的城市。同时，统计每个城市的用户数量，并按平均月收入降序排序结果。"

因此代理尝试从数据库获取信息，第一次尝试是错误的，所以代理再次尝试并获取正确的信息，然后传递给下一个代理。

![alt text](https://github.com/crewAIInc/crewAI-tools/blob/main/crewai_tools/tools/nl2sql/images/image-2.png?raw=true)
![alt text](https://github.com/crewAIInc/crewAI-tools/raw/main/crewai_tools/tools/nl2sql/images/image-3.png)

第二个任务目标是：

"审查数据并创建详细报告，然后根据提供的数据在数据库中创建包含相应字段的表。
包含每个城市的平均、最高和最低月收入信息，但只包含拥有多个用户的城市。同时，统计每个城市的用户数量，并按平均月收入降序排序结果。"

现在情况开始变得有趣，代理生成的 SQL 查询不仅创建了表，还将数据插入到表中。最后，代理仍然返回最终报告，该报告与数据库中的内容完全一致。

![alt text](https://github.com/crewAIInc/crewAI-tools/raw/main/crewai_tools/tools/nl2sql/images/image-4.png)
![alt text](https://github.com/crewAIInc/crewAI-tools/raw/main/crewai_tools/tools/nl2sql/images/image-5.png)

![alt text](https://github.com/crewAIInc/crewAI-tools/raw/main/crewai_tools/tools/nl2sql/images/image-9.png)
![alt text](https://github.com/crewAIInc/crewAI-tools/raw/main/crewai_tools/tools/nl2sql/images/image-7.png)

这是一个简单的示例，展示了 NL2SQLTool 如何用于与数据库交互并基于数据库中的数据生成报告。

该工具为代理的逻辑以及它如何与数据库交互提供了无限可能。

```md  theme={null}
 数据库 -> 代理 -> ... -> 代理 -> 数据库
```