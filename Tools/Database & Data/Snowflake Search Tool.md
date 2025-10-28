# Snowflake 搜索工具

> `SnowflakeSearchTool` 使 CrewAI 智能体能够执行 SQL 查询并对 Snowflake 数据仓库进行语义搜索。

# `SnowflakeSearchTool`

## 描述

`SnowflakeSearchTool` 旨在连接到 Snowflake 数据仓库并执行 SQL 查询，具有连接池、重试逻辑和异步执行等高级功能。该工具允许 CrewAI 智能体与 Snowflake 数据库交互，非常适合需要访问存储在 Snowflake 中的企业数据的数据分析、报告和商业智能任务。

## 安装

要使用此工具，您需要安装所需的依赖项：

```shell  theme={null}
uv add cryptography snowflake-connector-python snowflake-sqlalchemy
```

或者：

```shell  theme={null}
uv sync --extra snowflake
```

## 入门步骤

要有效使用 `SnowflakeSearchTool`，请按照以下步骤操作：

1. **安装依赖项**：使用上述命令之一安装所需的包。
2. **配置 Snowflake 连接**：使用您的 Snowflake 凭据创建 `SnowflakeConfig` 对象。
3. **初始化工具**：使用必要的配置创建工具实例。
4. **执行查询**：使用工具针对您的 Snowflake 数据库运行 SQL 查询。

## 示例

以下示例演示如何使用 `SnowflakeSearchTool` 从 Snowflake 数据库查询数据：

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import SnowflakeSearchTool, SnowflakeConfig

# 创建 Snowflake 配置
config = SnowflakeConfig(
    account="your_account",
    user="your_username",
    password="your_password",
    warehouse="COMPUTE_WH",
    database="your_database",
    snowflake_schema="your_schema"
)

# 初始化工具
snowflake_tool = SnowflakeSearchTool(config=config)

# 定义使用该工具的智能体
data_analyst_agent = Agent(
    role="数据分析师",
    goal="分析 Snowflake 数据库中的数据",
    backstory="一位能够从企业数据中提取见解的专家数据分析师。",
    tools=[snowflake_tool],
    verbose=True,
)

# 查询销售数据的示例任务
query_task = Task(
    description="查询上一季度的销售数据，并按收入汇总前5名产品。",
    expected_output="上一季度按收入排名的前5名产品的汇总报告。",
    agent=data_analyst_agent,
)

# 创建并运行团队
crew = Crew(agents=[data_analyst_agent], 
            tasks=[query_task])
result = crew.kickoff()
```

您还可以使用其他参数自定义工具：

```python Code theme={null}
# 使用自定义参数初始化工具
snowflake_tool = SnowflakeSearchTool(
    config=config,
    pool_size=10,
    max_retries=5,
    retry_delay=2.0,
    enable_caching=True
)
```

## 参数

### SnowflakeConfig 参数

`SnowflakeConfig` 类接受以下参数：

* **account**：必需。Snowflake 账户标识符。
* **user**：必需。Snowflake 用户名。
* **password**：可选*。Snowflake 密码。
* **private\_key\_path**：可选*。私钥文件路径（密码的替代方案）。
* **warehouse**：必需。Snowflake 仓库名称。
* **database**：必需。默认数据库。
* **snowflake\_schema**：必需。默认架构。
* **role**：可选。Snowflake 角色。
* **session\_parameters**：可选。自定义会话参数，以字典形式提供。

*必须提供 `password` 或 `private_key_path` 中的任意一个。

### SnowflakeSearchTool 参数

`SnowflakeSearchTool` 在初始化期间接受以下参数：

* **config**：必需。包含连接详细信息的 `SnowflakeConfig` 对象。
* **pool\_size**：可选。池中的连接数。默认为 5。
* **max\_retries**：可选。失败查询的最大重试尝试次数。默认为 3。
* **retry\_delay**：可选。重试之间的延迟（以秒为单位）。默认为 1.0。
* **enable\_caching**：可选。是否启用查询结果缓存。默认为 True。

## 用法

使用 `SnowflakeSearchTool` 时，您需要提供以下参数：

* **query**：必需。要执行的 SQL 查询。
* **database**：可选。覆盖配置中指定的默认数据库。
* **snowflake\_schema**：可选。覆盖配置中指定的默认架构。
* **timeout**：可选。查询超时时间（以秒为单位）。默认为 300。

该工具将以字典列表的形式返回查询结果，其中每个字典代表一行，列名作为键。

```python Code theme={null}
# 在智能体中使用工具的示例
data_analyst = Agent(
    role="数据分析师",
    goal="分析来自 Snowflake 的销售数据",
    backstory="一位具有 SQL 和数据可视化经验的专家数据分析师。",
    tools=[snowflake_tool],
    verbose=True
)

# 智能体将使用带有如下参数的工具：
# query="SELECT product_name, SUM(revenue) as total_revenue FROM sales GROUP BY product_name ORDER BY total_revenue DESC LIMIT 5"
# timeout=600

# 为智能体创建任务
analysis_task = Task(
    description="查询销售数据库并确定上一季度按收入排名的前5名产品。",
    expected_output="对按收入排名的前5名产品的详细分析。",
    agent=data_analyst
)

# 运行任务
crew = Crew(
    agents=[data_analyst], 
    tasks=[analysis_task]
)
result = crew.kickoff()
```

## 高级功能

### 连接池

`SnowflakeSearchTool` 实现了连接池，通过重用数据库连接来提高性能。您可以使用 `pool_size` 参数控制池大小。

### 自动重试

该工具会自动重试失败的查询，并采用指数退避策略。您可以使用 `max_retries` 和 `retry_delay` 参数配置重试行为。

### 查询结果缓存

为了提高重复查询的性能，工具可以缓存查询结果。此功能默认启用，但可以通过设置 `enable_caching=False` 来禁用。

### 密钥对认证

除了密码认证外，该工具还支持密钥对认证以增强安全性：

```python Code theme={null}
config = SnowflakeConfig(
    account="your_account",
    user="your_username",
    private_key_path="/path/to/your/private/key.p8",
    warehouse="COMPUTE_WH",
    database="your_database",
    snowflake_schema="your_schema"
)
```

## 错误处理

`SnowflakeSearchTool` 包括针对常见 Snowflake 问题的全面错误处理：

* 连接失败
* 查询超时
* 认证错误
* 数据库和架构错误

当发生错误时，工具将尝试重试操作（如果已配置）并提供详细的错误信息。

## 结论

`SnowflakeSearchTool` 提供了一种将 Snowflake 数据仓库与 CrewAI 智能体集成的强大方式。凭借连接池、自动重试和查询缓存等功能，它能够高效可靠地访问企业数据。该工具对于需要访问存储在 Snowflake 中的结构化数据的分析、报告和商业智能任务特别有用。