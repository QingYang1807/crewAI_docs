# SingleStore 搜索工具

> `SingleStoreSearchTool` 可安全地使用连接池在 SingleStore 上执行 SELECT/SHOW 查询。

# `SingleStoreSearchTool`

## 描述

针对 SingleStore 执行只读查询（`SELECT`/`SHOW`），具有连接池和输入验证功能。

## 安装

```shell  theme={null}
uv add crewai-tools[singlestore]
```

## 环境变量

可以使用 `SINGLESTOREDB_HOST`、`SINGLESTOREDB_USER`、`SINGLESTOREDB_PASSWORD` 等变量，或者使用 `SINGLESTOREDB_URL` 作为单一 DSN。

从 SingleStore 控制面板生成 API 密钥，[文档链接](https://docs.singlestore.com/cloud/reference/management-api/#generate-an-api-key)。

## 示例

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import SingleStoreSearchTool

tool = SingleStoreSearchTool(
    tables=["products"], 
    host="host", 
    user="user", 
    password="pass", 
    database="db",
)

agent = Agent(
    role="Analyst", 
    goal="Query SingleStore", 
    tools=[tool], 
    verbose=True,
)

task = Task(
    description="List 5 products", 
    expected_output="5 rows as JSON/text", 
    agent=agent,
)

crew = Crew(
    agents=[agent], 
    tasks=[task],
    verbose=True,
)

result = crew.kickoff()
```