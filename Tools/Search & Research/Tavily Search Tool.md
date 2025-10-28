# Tavily 搜索工具

> 使用 Tavily 搜索 API 执行全面的网络搜索

`TavilySearchTool` 提供了与 Tavily 搜索 API 的接口，使 CrewAI 代理能够执行全面的网络搜索。它允许指定搜索深度、主题、时间范围、包含/排除的域名，以及是否在结果中包含直接答案、原始内容或图像。

## 安装

要使用 `TavilySearchTool`，您需要安装 `tavily-python` 库：

```shell  theme={null}
pip install 'crewai[tools]' tavily-python
```

## 环境变量

确保您的 Tavily API 密钥已设置为环境变量：

```bash  theme={null}
export TAVILY_API_KEY='your_tavily_api_key'
```

在 [https://app.tavily.com/](https://app.tavily.com/) 获取 API 密钥（注册，然后创建密钥）。

## 使用示例

以下是在 CrewAI 代理中初始化和使用 `TavilySearchTool` 的方法：

```python  theme={null}
import os
from crewai import Agent, Task, Crew
from crewai_tools import TavilySearchTool

# 确保 TAVILY_API_KEY 环境变量已设置
# os.environ["TAVILY_API_KEY"] = "YOUR_TAVILY_API_KEY"

# 初始化工具
tavily_tool = TavilySearchTool()

# 创建一个使用该工具的代理
researcher = Agent(
    role='市场研究员',
    goal='查找有关最新 AI 趋势的信息',
    backstory='一位专门研究技术的专业市场研究员。',
    tools=[tavily_tool],
    verbose=True
)

# 为代理创建任务
research_task = Task(
    description='搜索 2024 年前三大 AI 趋势。',
    expected_output='一份总结所发现的前三大 AI 趋势的 JSON 报告。',
    agent=researcher
)

# 组建团队并启动
crew = Crew(
    agents=[researcher],
    tasks=[research_task],
    verbose=2
)

result = crew.kickoff()
print(result)
```

## 配置选项

`TavilySearchTool` 在初始化或调用 `run` 方法时接受以下参数：

* `query` (str)：**必需**。搜索查询字符串。
* `search_depth` (Literal\["basic", "advanced"], optional)：搜索深度。默认为 `"basic"`。
* `topic` (Literal\["general", "news", "finance"], optional)：搜索重点主题。默认为 `"general"`。
* `time_range` (Literal\["day", "week", "month", "year"], optional)：搜索时间范围。默认为 `None`。
* `days` (int, optional)：要回溯搜索的天数。当未设置 `time_range` 时相关。默认为 `7`。
* `max_results` (int, optional)：返回的最大搜索结果数。默认为 `5`。
* `include_domains` (Sequence\[str], optional)：在搜索中优先考虑的域名列表。默认为 `None`。
* `exclude_domains` (Sequence\[str], optional)：从搜索中排除的域名列表。默认为 `None`。
* `include_answer` (Union\[bool, Literal\["basic", "advanced"]], optional)：是否包含根据搜索结果合成的直接答案。默认为 `False`。
* `include_raw_content` (bool, optional)：是否包含所搜索页面的原始 HTML 内容。默认为 `False`。
* `include_images` (bool, optional)：是否包含图像结果。默认为 `False`。
* `timeout` (int, optional)：请求超时时间（以秒为单位）。默认为 `60`。

## 高级用法

您可以使用自定义参数配置工具：

```python  theme={null}
# 示例：使用特定参数初始化
custom_tavily_tool = TavilySearchTool(
    search_depth='advanced',
    max_results=10,
    include_answer=True
)

# 代理将使用这些默认值
agent_with_custom_tool = Agent(
    role="高级研究员",
    goal="进行具有全面结果的详细研究",
    tools=[custom_tavily_tool]
)
```

## 功能特点

* **全面搜索**：访问 Tavily 强大的搜索索引
* **可配置深度**：在基本和高级搜索模式之间选择
* **主题过滤**：将搜索重点放在一般、新闻或金融主题上
* **时间范围控制**：将结果限制在特定时间段内
* **域名控制**：包含或排除特定域名
* **直接答案**：从搜索结果中获取合成答案
* **内容过滤**：通过自动内容截断防止上下文窗口问题

## 响应格式

该工具将搜索结果作为 JSON 字符串返回，包含：

* 带有标题、URL 和内容片段的搜索结果
* 可选的查询直接答案
* 可选的图像结果
* 可选的原始 HTML 内容（启用时）

每个结果的内容会自动截断以防止上下文窗口问题，同时保留最相关的信息。