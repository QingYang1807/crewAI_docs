# SerpApi Google 购物工具

> `SerpApiGoogleShoppingTool` 使用 SerpApi 搜索 Google 购物结果。

# `SerpApiGoogleShoppingTool`

## 描述

利用 `SerpApiGoogleShoppingTool` 通过 SerpApi 查询 Google 购物，并检索以产品为导向的结果。

## 安装

```shell  theme={null}
uv add crewai-tools[serpapi]
```

## 环境变量

* `SERPAPI_API_KEY` (必需)：SerpApi 的 API 密钥。可在 [https://serpapi.com/](https://serpapi.com/) 创建（提供免费套餐）。

## 示例

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import SerpApiGoogleShoppingTool

tool = SerpApiGoogleShoppingTool()

agent = Agent(
    role="购物研究员",
    goal="查找相关产品",
    backstory="产品搜索专家",
    tools=[tool],
    verbose=True,
)

task = Task(
    description="在 Google 购物中搜索 '无线降噪耳机'",
    expected_output="最相关的产品列表，包含标题和链接",
    agent=agent,
)

crew = Crew(agents=[agent], tasks=[task])
result = crew.kickoff()
```

## 注意事项

* 在环境中设置 `SERPAPI_API_KEY`。可在 [https://serpapi.com/](https://serpapi.com/) 创建密钥。
* 另请参阅通过 SerpApi 的 Google 网络搜索工具：`/en/tools/search-research/serpapi-googlesearchtool`

## 参数

### 运行参数

* `search_query` (字符串，必需)：产品搜索查询。
* `location` (字符串，可选)：地理位置参数。