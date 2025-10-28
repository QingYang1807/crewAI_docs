# 推理

> 了解如何启用和使用Agent推理功能以改进任务执行。

## 概述

Agent推理是一项允许Agent在执行任务前对任务进行反思并创建计划的功能。这有助于Agent更有条理地处理任务，并确保它们已准备好执行分配的工作。

## 使用方法

要为Agent启用推理，只需在创建Agent时设置 `reasoning=True`：

```python
from crewai import Agent

agent = Agent(
    role="数据分析师",
    goal="分析复杂数据集并提供洞察",
    backstory="您是一名经验丰富的数据分析师，专长是在复杂数据中发现模式。",
    reasoning=True,  # 启用推理
    max_reasoning_attempts=3  # 可选：设置推理尝试的最大次数
)
```

## 工作原理

启用推理后，Agent在执行任务前会：

1. 对任务进行反思并创建详细计划
2. 评估是否准备好执行任务
3. 必要时完善计划，直到准备就绪或达到 max_reasoning_attempts
4. 在执行前将推理计划注入任务描述

这个过程帮助Agent将复杂任务分解为可管理的步骤，并在开始前识别潜在的挑战。

## 配置选项

<ParamField body="reasoning" type="bool" default="False">
  启用或禁用推理
</ParamField>

<ParamField body="max_reasoning_attempts" type="int" default="None">
  在继续执行前完善计划的最大尝试次数。如果为 None（默认），Agent将持续完善直到准备就绪。
</ParamField>

## 示例

这是一个完整的示例：

```python
from crewai import Agent, Task, Crew

# 创建一个启用了推理的Agent
analyst = Agent(
    role="数据分析师",
    goal="分析数据并提供洞察",
    backstory="您是一名专业的数据分析师。",
    reasoning=True,
    max_reasoning_attempts=3  # 可选：设置推理尝试的限制
)

# 创建一个任务
analysis_task = Task(
    description="分析提供的销售数据并识别关键趋势。",
    expected_output="一份突出前三大销售趋势的报告。",
    agent=analyst
)

# 创建一个团队并运行任务
crew = Crew(agents=[analyst], tasks=[analysis_task])
result = crew.kickoff()

print(result)
```

## 错误处理

推理过程设计为具有鲁棒性，内置了错误处理。如果在推理过程中发生错误，Agent将不使用推理计划继续执行任务。这确保即使推理过程失败，任务仍能被执行。

以下是如何在代码中处理潜在错误：

```python
from crewai import Agent, Task
import logging

# 设置日志记录以捕获任何推理错误
logging.basicConfig(level=logging.INFO)

# 创建一个启用了推理的Agent
agent = Agent(
    role="数据分析师",
    goal="分析数据并提供洞察",
    reasoning=True,
    max_reasoning_attempts=3
)

# 创建一个任务
task = Task(
    description="分析提供的销售数据并识别关键趋势。",
    expected_output="一份突出前三大销售趋势的报告。",
    agent=agent
)

# 执行任务
# 如果在推理过程中发生错误，将记录错误并继续执行
result = agent.execute_task(task)
```

## 推理输出示例

以下是数据分析任务的推理计划可能的样子：

```
任务：分析提供的销售数据并识别关键趋势。

推理计划：
我将分析销售数据以识别前三大趋势。

1. 对任务的理解：
   我需要分析销售数据，识别对业务决策有价值的关键趋势。

2. 我将采取的关键步骤：
   - 首先，我将检查数据结构以了解可用的字段
   - 然后，我将执行探索性数据分析以识别模式
   - 接下来，我将按时间段分析销售数据以识别时间趋势
   - 我还将按产品类别和客户细分分析销售情况
   - 最后，我将识别前三大最显著的趋势

3. 应对挑战的方法：
   - 如果数据有缺失值，我将决定是填充还是过滤它们
   - 如果数据有异常值，我将调查它们是有效数据点还是错误
   - 如果趋势不明显，我将应用统计方法来发现模式

4. 可用工具的使用：
   - 我将使用数据分析工具来探索和可视化数据
   - 我将使用统计工具来识别显著模式
   - 我将使用知识检索来访问有关销售分析的相关信息

5. 预期结果：
   一份简洁的报告，突出前三大销售趋势，并提供来自数据的支持证据。

准备就绪：我已准备好执行此任务。
```

这个推理计划帮助Agent组织其任务处理方法，考虑潜在挑战，并确保提供预期的输出。