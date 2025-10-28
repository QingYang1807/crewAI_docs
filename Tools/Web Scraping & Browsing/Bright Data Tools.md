# Bright Data 工具

> 用于 SERP 搜索、Web Unlocker 抓取和 Dataset API 的 Bright Data 集成工具。

# Bright Data 工具

这套工具集成了 Bright Data 服务，用于网页数据提取。

## 安装

```shell  theme={null}
uv add crewai-tools requests aiohttp
```

## 环境变量

* `BRIGHT_DATA_API_KEY`（必需）
* `BRIGHT_DATA_ZONE`（用于 SERP/Web Unlocker）

在 [https://brightdata.com/](https://brightdata.com/) 创建凭证（注册后创建 API 令牌和区域）。
查看他们的文档：[https://developers.brightdata.com/](https://developers.brightdata.com/)

## 包含的工具

* `BrightDataSearchTool`：支持地理位置/语言/设备选项的 SERP 搜索（Google/Bing/Yandex）。
* `BrightDataWebUnlockerTool`：具有反机器人绕过和渲染功能的页面抓取。
* `BrightDataDatasetTool`：运行 Dataset API 作业并获取结果。

## 示例

### SERP 搜索

```python Code theme={null}
from crewai_tools import BrightDataSearchTool

tool = BrightDataSearchTool(
    query="CrewAI", 
    country="us",
)

print(tool.run())
```

### Web Unlocker

```python Code theme={null}
from crewai_tools import BrightDataWebUnlockerTool

tool = BrightDataWebUnlockerTool(
    url="https://example.com", 
    format="markdown",
)

print(tool.run(url="https://example.com"))
```

### Dataset API

```python Code theme={null}
from crewai_tools import BrightDataDatasetTool

tool = BrightDataDatasetTool(
    dataset_type="ecommerce", 
    url="https://example.com/product",
)

print(tool.run())
```

## 故障排除

* 401/403 错误：验证 `BRIGHT_DATA_API_KEY` 和 `BRIGHT_DATA_ZONE`。
* 内容为空/被阻止：启用渲染或尝试不同的区域。

## 示例

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import BrightDataSearchTool

tool = BrightDataSearchTool(
    query="CrewAI", 
    country="us",
)

agent = Agent(
    role="Web Researcher",
    goal="Search with Bright Data",
    backstory="Finds reliable results",
    tools=[tool],
    verbose=True,
)

task = Task(
    description="Search for CrewAI and summarize top results",
    expected_output="Short summary with links",
    agent=agent,
)

crew = Crew(
    agents=[agent], 
    tasks=[task],
    verbose=True,
)

result = crew.kickoff()
```