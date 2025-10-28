# Databricks SQL 查询工具

> `DatabricksQueryTool` 用于对 Databricks 工作区表执行 SQL 查询。

# `DatabricksQueryTool`

## 描述

使用 CLI 配置文件或直接的主机/令牌认证对 Databricks 工作区表运行 SQL。

## 安装

```shell  theme={null}
uv add crewai-tools[databricks-sdk]
```

## 环境变量

* `DATABRICKS_CONFIG_PROFILE` 或 (`DATABRICKS_HOST` + `DATABRICKS_TOKEN`)

在 Databricks 工作区的用户设置 → 开发者下创建个人访问令牌并查找主机详细信息。
文档：[https://docs.databricks.com/en/dev-tools/auth/pat.html](https://docs.databricks.com/en/dev-tools/auth/pat.html)

## 示例

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import DatabricksQueryTool

tool = DatabricksQueryTool(
    default_catalog="main", 
    default_schema="default",
)

agent = Agent(
    role="Data Analyst",
    goal="Query Databricks",
    tools=[tool],
    verbose=True,
)

task = Task(
    description="SELECT * FROM my_table LIMIT 10",
    expected_output="10 rows", 
    agent=agent,
)

crew = Crew(
    agents=[agent], 
    tasks=[task],
    verbose=True,
)
result = crew.kickoff()

print(result)
```

## 参数

* `query` (必需)：要执行的 SQL 查询
* `catalog` (可选)：覆盖默认目录
* `db_schema` (可选)：覆盖默认架构
* `warehouse_id` (可选)：覆盖默认 SQL 仓库
* `row_limit` (可选)：返回的最大行数（默认：1000）

## 初始化时的默认值

* `default_catalog`
* `default_schema`
* `default_warehouse_id`

### 错误处理与提示

* 认证错误：验证 `DATABRICKS_HOST` 以 `https://` 开头且令牌有效。
* 权限：确保您的 SQL 仓库和架构可被您的令牌访问。
* 限制：在代理循环中应避免长时间运行的查询；添加筛选器/限制。