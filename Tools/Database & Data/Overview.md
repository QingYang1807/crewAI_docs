# 概述

> 连接数据库、向量存储和数据仓库，实现全面的数据访问

这些工具使您的代理能够与各种数据库系统交互，从传统的 SQL 数据库到现代的向量存储和数据仓库。

## **可用工具**

<CardGroup cols={2}>
  <Card title="MySQL 工具" icon="database" href="/en/tools/database-data/mysqltool">
    使用 SQL 操作连接并查询 MySQL 数据库。
  </Card>

  <Card title="PostgreSQL 搜索" icon="elephant" href="/en/tools/database-data/pgsearchtool">
    高效搜索和查询 PostgreSQL 数据库。
  </Card>

  <Card title="Snowflake 搜索" icon="snowflake" href="/en/tools/database-data/snowflakesearchtool">
    访问 Snowflake 数据仓库进行分析和报告。
  </Card>

  <Card title="自然语言转 SQL 工具" icon="language" href="/en/tools/database-data/nl2sqltool">
    自动将自然语言查询转换为 SQL 语句。
  </Card>

  <Card title="Qdrant 向量搜索" icon="vector-square" href="/en/tools/database-data/qdrantvectorsearchtool">
    使用 Qdrant 向量数据库搜索向量嵌入。
  </Card>

  <Card title="Weaviate 向量搜索" icon="network-wired" href="/en/tools/database-data/weaviatevectorsearchtool">
    使用 Weaviate 向量数据库执行语义搜索。
  </Card>

  <Card title="MongoDB 向量搜索" icon="leaf" href="/en/tools/database-data/mongodbvectorsearchtool">
    在 MongoDB Atlas 上使用索引辅助工具进行向量相似性搜索。
  </Card>

  <Card title="SingleStore 搜索" icon="database" href="/en/tools/database-data/singlestoresearchtool">
    在 SingleStore 上执行安全的 SELECT/SHOW 查询，具有连接池和验证功能。
  </Card>
</CardGroup>

## **常见用例**

* **数据分析**：查询数据库以获取商业智能和报告
* **向量搜索**：使用语义嵌入查找相似内容
* **ETL 操作**：在系统之间提取、转换和加载数据
* **实时分析**：访问实时数据以支持决策

```python  theme={null}
from crewai_tools import MySQLTool, QdrantVectorSearchTool, NL2SQLTool

# 创建数据库工具
mysql_db = MySQLTool()
vector_search = QdrantVectorSearchTool()
nl_to_sql = NL2SQLTool()

# 添加到您的代理
agent = Agent(
    role="数据分析师",
    tools=[mysql_db, vector_search, nl_to_sql],
    goal="从各种数据源中提取洞察"
)
```