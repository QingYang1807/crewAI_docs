# 异步启动 Crew

> 异步启动 Crew

## 简介

CrewAI 提供了异步启动 crew 的功能，允许您以非阻塞的方式启动 crew 执行。
当您需要并发运行多个 crew，或在 crew 执行期间需要执行其他任务时，此功能特别有用。

## 异步 Crew 执行

要异步启动 crew，请使用 `kickoff_async()` 方法。此方法在单独的线程中启动 crew 执行，允许主线程继续执行其他任务。

### 方法签名

```python Code theme={null}
def kickoff_async(self, inputs: dict) -> CrewOutput:
```

### 参数

* `inputs` (dict)：包含任务所需输入数据的字典。

### 返回值

* `CrewOutput`：表示 crew 执行结果的对象。

## 潜在用例

* **并行内容生成**：异步启动多个独立的 crew，每个 crew 负责生成不同主题的内容。例如，一个 crew 可以研究和起草关于 AI 趋势的文章，而另一个 crew 则生成关于新产品发布的社交媒体帖子。每个 crew 独立运行，从而实现高效的内容规模化生产。

* **并发市场研究任务**：异步启动多个 crew 以并行执行市场研究。一个 crew 可能分析行业趋势，另一个审查竞争对手策略，还有一个评估消费者情绪。每个 crew 独立完成任务，从而能够更快、更全面地获得洞察。

* **独立的旅行规划模块**：执行独立的 crew 来分别规划旅行的不同方面。一个 crew 可能处理航班选项，另一个负责住宿，第三个规划活动。每个 crew 异步工作，允许旅行的各个组件同时且独立地进行规划，从而更快地获得结果。

## 示例：单个异步 Crew 执行

以下是如何使用 asyncio 异步启动 crew 并等待结果的示例：

```python Code theme={null}
import asyncio
from crewai import Crew, Agent, Task

# 创建一个启用了代码执行的 agent
coding_agent = Agent(
    role="Python 数据分析师",
    goal="使用 Python 分析数据并提供洞察",
    backstory="您是一名经验丰富的数据分析师，具备强大的 Python 技能。",
    allow_code_execution=True
)

# 创建一个需要代码执行的任务
data_analysis_task = Task(
    description="分析给定数据集并计算参与者的平均年龄。年龄：{ages}",
    agent=coding_agent,
    expected_output="参与者的平均年龄。"
)

# 创建一个 crew 并添加任务
analysis_crew = Crew(
    agents=[coding_agent],
    tasks=[data_analysis_task]
)

# 异步函数，用于异步启动 crew
async def async_crew_execution():
    result = await analysis_crew.kickoff_async(inputs={"ages": [25, 30, 35, 40, 45]})
    print("Crew 结果:", result)

# 运行异步函数
asyncio.run(async_crew_execution())
```

## 示例：多个异步 Crew 执行

在本示例中，我们将展示如何异步启动多个 crew 并使用 `asyncio.gather()` 等待它们全部完成：

```python Code theme={null}
import asyncio
from crewai import Crew, Agent, Task

# 创建一个启用了代码执行的 agent
coding_agent = Agent(
    role="Python 数据分析师",
    goal="使用 Python 分析数据并提供洞察",
    backstory="您是一名经验丰富的数据分析师，具备强大的 Python 技能。",
    allow_code_execution=True
)

# 创建需要代码执行的任务
task_1 = Task(
    description="分析第一个数据集并计算参与者的平均年龄。年龄：{ages}",
    agent=coding_agent,
    expected_output="参与者的平均年龄。"
)

task_2 = Task(
    description="分析第二个数据集并计算参与者的平均年龄。年龄：{ages}",
    agent=coding_agent,
    expected_output="参与者的平均年龄。"
)

# 创建两个 crew 并添加任务
crew_1 = Crew(agents=[coding_agent], tasks=[task_1])
crew_2 = Crew(agents=[coding_agent], tasks=[task_2])

# 异步函数，用于异步启动多个 crew 并等待所有 crew 完成
async def async_multiple_crews():
    # 创建用于并发执行的协程
    result_1 = crew_1.kickoff_async(inputs={"ages": [25, 30, 35, 40, 45]})
    result_2 = crew_2.kickoff_async(inputs={"ages": [20, 22, 24, 28, 30]})

    # 等待两个 crew 都完成
    results = await asyncio.gather(result_1, result_2)

    for i, result in enumerate(results, 1):
        print(f"Crew {i} 结果:", result)

# 运行异步函数
asyncio.run(async_multiple_crews())
```