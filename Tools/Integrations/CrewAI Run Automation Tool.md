# CrewAI 运行自动化工具

> 使 CrewAI 智能体能够调用 CrewAI 平台自动化，并在您的工作流中利用外部团队服务。

# `InvokeCrewAIAutomationTool`

`InvokeCrewAIAutomationTool` 提供了 CrewAI 平台 API 与外部团队服务的集成。该工具允许您从 CrewAI 智能体内部调用并与之交互 CrewAI 平台自动化，实现不同团队工作流之间的无缝集成。

## 安装

```bash  theme={null}
uv pip install 'crewai[tools]'
```

## 要求

* CrewAI 平台 API 访问权限
* 用于身份验证的有效承载令牌
* 对 CrewAI 平台自动化端点的网络访问权限

## 使用方法

以下是如何在 CrewAI 智能体中使用该工具：

```python {2, 4-9} theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import InvokeCrewAIAutomationTool

# 初始化工具
automation_tool = InvokeCrewAIAutomationTool(
    crew_api_url="https://data-analysis-crew-[...].crewai.com",
    crew_bearer_token="your_bearer_token_here",
    crew_name="Data Analysis Crew",
    crew_description="分析数据并生成洞察"
)

# 创建使用该工具的 CrewAI 智能体
automation_coordinator = Agent(
    role='Automation Coordinator',
    goal='协调并执行自动化的团队任务',
    backstory='我是利用自动化工具执行复杂工作流程的专家。',
    tools=[automation_tool],
    verbose=True
)

# 为智能体创建任务
analysis_task = Task(
    description="执行数据分析自动化并提供洞察",
    agent=automation_coordinator,
    expected_output="全面的数据分析报告"
)

# 创建包含智能体的团队
crew = Crew(
    agents=[automation_coordinator],
    tasks=[analysis_task],
    verbose=2
)

# 运行团队
result = crew.kickoff()
print(result)
```

## 工具参数

| 参数 | 类型 | 必需 | 默认值 | 描述 |
| :---------------------- | :----- | :------- | :------ | :-------------------------------------------------- |
| **crew\_api\_url** | `str` | 是 | None | CrewAI 平台自动化 API 的基础 URL |
| **crew\_bearer\_token** | `str` | 是 | None | 用于 API 身份验证的承载令牌 |
| **crew\_name** | `str` | 是 | None | 团队自动化的名称 |
| **crew\_description** | `str` | 是 | None | 团队自动化功能的描述 |
| **max\_polling\_time** | `int` | 否 | 600 | 等待任务完成的最长时间（秒） |
| **crew\_inputs** | `dict` | 否 | None | 定义自定义输入模式字段的字典 |

## 环境变量

```bash  theme={null}
CREWAI_API_URL=https://your-crew-automation.crewai.com  # 替代传递 crew_api_url
CREWAI_BEARER_TOKEN=your_bearer_token_here              # 替代传递 crew_bearer_token
```

## 高级用法

### 使用动态参数的自定义输入模式

```python {2, 4-15} theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import InvokeCrewAIAutomationTool
from pydantic import Field

# 定义自定义输入模式
custom_inputs = {
    "year": Field(..., description="检索报告的年份（整数）"),
    "region": Field(default="global", description="分析的地理区域"),
    "format": Field(default="summary", description="报告格式（summary、detailed、raw）")
}

# 创建具有自定义输入的工具
market_research_tool = InvokeCrewAIAutomationTool(
    crew_api_url="https://state-of-ai-report-crew-[...].crewai.com",
    crew_bearer_token="your_bearer_token_here",
    crew_name="State of AI Report",
    crew_description="检索给定年份和地区的人工智能状态综合报告",
    crew_inputs=custom_inputs,
    max_polling_time=15 * 60  # 15分钟超时
)

# 创建使用该工具的智能体
research_agent = Agent(
    role="Research Coordinator",
    goal="协调并执行市场研究任务",
    backstory="您是协调研究任务和利用自动化工具的专家。",
    tools=[market_research_tool],
    verbose=True
)

# 创建并执行带有自定义参数的任务
research_task = Task(
    description="对2024年北美地区的人工智能工具市场进行详细格式的研究",
    agent=research_agent,
    expected_output="综合市场研究报告"
)

crew = Crew(
    agents=[research_agent],
    tasks=[research_task]
)

result = crew.kickoff()
```

### 多阶段自动化工作流

```python {2, 4-35} theme={null}
from crewai import Agent, Task, Crew, Process
from crewai_tools import InvokeCrewAIAutomationTool

# 初始化不同的自动化工具
data_collection_tool = InvokeCrewAIAutomationTool(
    crew_api_url="https://data-collection-crew-[...].crewai.com",
    crew_bearer_token="your_bearer_token_here",
    crew_name="Data Collection Automation",
    crew_description="收集和预处理原始数据"
)

analysis_tool = InvokeCrewAIAutomationTool(
    crew_api_url="https://analysis-crew-[...].crewai.com",
    crew_bearer_token="your_bearer_token_here",
    crew_name="Analysis Automation",
    crew_description="执行高级数据分析和建模"
)

reporting_tool = InvokeCrewAIAutomationTool(
    crew_api_url="https://reporting-crew-[...].crewai.com",
    crew_bearer_token="your_bearer_token_here",
    crew_name="Reporting Automation",
    crew_description="生成综合报告和可视化"
)

# 创建专业化智能体
data_collector = Agent(
    role='Data Collection Specialist',
    goal='从各种来源收集和预处理数据',
    backstory='我专注于从多个来源收集和清理数据。',
    tools=[data_collection_tool]
)

data_analyst = Agent(
    role='Data Analysis Expert',
    goal='对收集的数据执行高级分析',
    backstory='我是统计分析和机器学习专家。',
    tools=[analysis_tool]
)

report_generator = Agent(
    role='Report Generation Specialist',
    goal='创建综合报告和可视化',
    backstory='我擅长从复杂数据创建清晰、可操作的报告。',
    tools=[reporting_tool]
)

# 创建顺序任务
collection_task = Task(
    description="收集2024年第四季度分析的市场数据",
    agent=data_collector
)

analysis_task = Task(
    description="分析收集的数据以识别趋势和模式",
    agent=data_analyst
)

reporting_task = Task(
    description="生成包含关键见解和建议的高管摘要报告",
    agent=report_generator
)

# 创建具有顺序处理的团队
crew = Crew(
    agents=[data_collector, data_analyst, report_generator],
    tasks=[collection_task, analysis_task, reporting_task],
    process=Process.sequential,
    verbose=2
)

result = crew.kickoff()
```

## 用例

### 分布式团队协调

* 协调多个专业化团队自动化，处理复杂的多阶段工作流
* 实现不同自动化服务之间的无缝交接，以全面执行任务
* 通过在多个 CrewAI 平台自动化之间分配工作负载来扩展处理能力

### 跨平台集成

* 将 CrewAI 智能体与 CrewAI 平台自动化桥接，实现混合本地-云工作流
* 利用专业化自动化，同时保持本地控制和协调
* 实现本地智能体与基于云的自动化服务之间的安全协作

### 企业自动化管道

* 创建企业级自动化管道，结合本地智能和云处理能力
* 实现跨越多个自动化服务的复杂业务工作流
* 为数据分析、报告和决策制定实现可扩展、可重复的流程

### 动态工作流组合

* 根据任务需求通过链接不同的自动化服务来动态组合工作流
* 实现自适应处理，其中自动化的选择取决于数据特性或业务规则
* 创建灵活、可重用的自动化组件，可以以各种方式组合

### 专业化领域处理

* 从通用智能体访问特定领域的自动化（财务分析、法律研究、技术文档）
* 利用预先构建的、专业化的团队自动化，而无需重建复杂的领域逻辑
* 使智能体能够通过目标自动化服务访问专家级功能

## 自定义输入模式

定义 `crew_inputs` 时，使用 Pydantic Field 对象指定输入参数：

```python  theme={null}
from pydantic import Field

crew_inputs = {
    "required_param": Field(..., description="此参数是必需的"),
    "optional_param": Field(default="default_value", description="此参数是可选的"),
    "typed_param": Field(..., description="整数参数", ge=1, le=100)  # 带有验证
}
```

## 错误处理

该工具为常见场景提供全面的错误处理：

* **API 连接错误**：与 CrewAI 平台的网络连接问题
* **身份验证错误**：无效或过期的承载令牌
* **超时错误**：超过最大轮询时间的任务
* **任务失败**：执行期间失败的团队自动化
* **输入验证错误**：传递给自动化端点的无效参数

## API 端点

该工具与两个主要 API 端点交互：

* `POST {crew_api_url}/kickoff`：启动新的团队自动化任务
* `GET {crew_api_url}/status/{crew_id}`：检查运行中任务的状态

## 注意事项

* 该工具每秒自动轮询状态端点，直到完成或超时
* 成功的任务直接返回结果，而失败的任务返回错误信息
* 承载令牌应保持安全，不应在生产环境中硬编码
* 考虑对承载令牌等敏感配置使用环境变量
* 自定义输入模式必须与目标团队自动化的预期参数兼容