# SerpApi Google搜索工具

> `SerpApiGoogleSearchTool`使用SerpApi服务执行Google搜索。

# `SerpApiGoogleSearchTool`

## 描述

使用`SerpApiGoogleSearchTool`通过SerpApi运行Google搜索并检索结构化结果。需要SerpApi API密钥。

## 安装

```shell  theme={null}
uv add crewai-tools[serpapi]
```

## 环境变量

* `SERPAPI_API_KEY`（必需）：SerpApi的API密钥。可在[https://serpapi.com/](https://serpapi.com/)创建（提供免费层级）。

## 示例

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import SerpApiGoogleSearchTool

tool = SerpApiGoogleSearchTool()

agent = Agent(
    role="研究员",
    goal="使用Google搜索回答问题",
    backstory="搜索专家",
    tools=[tool],
    verbose=True,
)

task = Task(
    description="搜索最新的CrewAI发布版本",
    expected_output="包含标题和链接的相关结果简明列表",
    agent=agent,
)

crew = Crew(agents=[agent], tasks=[task])
result = crew.kickoff()
```

## 注意事项

* 在环境中设置`SERPAPI_API_KEY`。可在[https://serpapi.com/](https://serpapi.com/)创建密钥。
* 另请参阅通过SerpApi进行Google购物搜索：`/en/tools/search-research/serpapi-googleshoppingtool`

## 参数

### 运行参数

* `search_query`（字符串，必需）：Google查询内容。
* `location`（字符串，可选）：地理位置参数。

## 注意事项

* 此工具封装了SerpApi并返回结构化搜索结果。