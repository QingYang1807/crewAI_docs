# Tavily 提取工具

> 使用 Tavily API 从网页中提取结构化内容

`TavilyExtractorTool` 允许 CrewAI 代理使用 Tavily API 从网页中提取结构化内容。它可以处理单个 URL 或 URL 列表，并提供控制提取深度和包含图像的选项。

## 安装

要使用 `TavilyExtractorTool`，您需要安装 `tavily-python` 库：

```shell  theme={null}
pip install 'crewai[tools]' tavily-python
```

您还需要将 Tavily API 密钥设置为环境变量：

```bash  theme={null}
export TAVILY_API_KEY='your-tavily-api-key'
```

## 使用示例

以下是在 CrewAI 代理中初始化和使用 `TavilyExtractorTool` 的方法：

```python  theme={null}
import os
from crewai import Agent, Task, Crew
from crewai_tools import TavilyExtractorTool

# 确保在您的环境中设置了 TAVILY_API_KEY
# os.environ["TAVILY_API_KEY"] = "YOUR_API_KEY"

# 初始化工具
tavily_tool = TavilyExtractorTool()

# 创建一个使用该工具的代理
extractor_agent = Agent(
    role='Web Content Extractor',
    goal='从指定网页中提取关键信息',
    backstory='您是一位使用 Tavily API 从网站中提取相关内容的专家。',
    tools=[tavily_tool],
    verbose=True
)

# 为代理定义一个任务
extract_task = Task(
    description='使用基础提取深度从 URL https://example.com 中提取主要内容。',
    expected_output='一个包含从 URL 中提取的内容的 JSON 字符串。',
    agent=extractor_agent
)

# 创建并运行团队
crew = Crew(
    agents=[extractor_agent],
    tasks=[extract_task],
    verbose=2
)

result = crew.kickoff()
print(result)
```

## 配置选项

`TavilyExtractorTool` 接受以下参数：

* `urls` (Union\[List\[str], str])：**必需**。要从中提取数据的单个 URL 字符串或 URL 字符串列表。
* `include_images` (Optional\[bool])：是否在提取结果中包含图像。默认为 `False`。
* `extract_depth` (Literal\["basic", "advanced"])：提取深度。使用 `"basic"` 进行更快的、表层的提取，或使用 `"advanced"` 进行更全面的提取。默认为 `"basic"`。
* `timeout` (int)：等待提取请求完成的最长时间（以秒为单位）。默认为 `60`。

## 高级用法

### 多 URL 高级提取

```python  theme={null}
# 多 URL 和高级提取的示例
multi_extract_task = Task(
    description='使用高级提取从 https://example.com 和 https://anotherexample.org 中提取内容。',
    expected_output='一个包含从两个 URL 中提取的内容的 JSON 字符串。',
    agent=extractor_agent
)

# 使用自定义参数配置工具
custom_extractor = TavilyExtractorTool(
    extract_depth='advanced',
    include_images=True,
    timeout=120
)

agent_with_custom_tool = Agent(
    role="Advanced Content Extractor",
    goal="提取包含图像的全面内容",
    tools=[custom_extractor]
)
```

### 工具参数

您可以在初始化时通过设置参数来自定义工具的行为：

```python  theme={null}
# 使用自定义配置初始化
extractor_tool = TavilyExtractorTool(
    extract_depth='advanced',  # 更全面的提取
    include_images=True,       # 包含图像结果
    timeout=90                 # 自定义超时
)
```

## 功能特性

* **单个或多个 URL**：可以从一个 URL 中提取内容，也可以在单个请求中处理多个 URL
* **可配置深度**：可选择基础（快速）或高级（全面）提取模式
* **图像支持**：可选择在提取结果中包含图像
* **结构化输出**：返回格式良好的 JSON，其中包含提取的内容
* **错误处理**：稳健地处理网络超时和提取错误

## 响应格式

该工具返回一个 JSON 字符串，表示从提供的 URL 中提取的结构化数据。确切的结构取决于页面的内容和所使用的 `extract_depth`。

常见的响应元素包括：

* **Title**：页面标题
* **Content**：页面的主要文本内容
* **Images**：图像 URL 和元数据（当 `include_images=True` 时）
* **Metadata**：其他页面信息，如作者、描述等

## 使用场景

* **内容分析**：从竞争对手网站中提取和分析内容
* **研究**：从多个来源收集结构化数据以进行分析
* **内容迁移**：从现有网站中提取内容以进行迁移
* **监控**：定期提取内容以进行变更检测
* **数据收集**：从网络源中系统地提取信息

有关响应结构和可用选项的详细信息，请参阅 [Tavily API 文档](https://docs.tavily.com/docs/tavily-api/python-sdk#extract)。