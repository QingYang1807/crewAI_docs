# 顺序流程

> CrewAI项目中利用顺序流程执行任务的综合指南。

## 简介

CrewAI提供了一个灵活的框架，以结构化的方式执行任务，支持顺序和分层流程。
本指南概述了如何有效实施这些流程，以确保高效的任务执行和项目完成。

## 顺序流程概述

顺序流程确保任务一个接一个地执行，遵循线性进展。
这种方法适用于需要按特定顺序完成任务的项目。

### 主要特点

* **线性任务流程**：通过按预定顺序处理任务，确保有序进展。
* **简单性**：最适合具有清晰、逐步任务的项目。
* **易于监控**：便于跟踪任务完成情况和项目进展。

## 实施顺序流程

要使用顺序流程，需要组建您的团队，并按照需要执行的顺序定义任务。

```python Code theme={null}
from crewai import Crew, Process, Agent, Task, TaskOutput, CrewOutput

# 定义您的代理
researcher = Agent(
  role='Researcher',
  goal='Conduct foundational research',
  backstory='An experienced researcher with a passion for uncovering insights'
)
analyst = Agent(
  role='Data Analyst',
  goal='Analyze research findings',
  backstory='A meticulous analyst with a knack for uncovering patterns'
)
writer = Agent(
  role='Writer',
  goal='Draft the final report',
  backstory='A skilled writer with a talent for crafting compelling narratives'
)

# 定义您的任务
research_task = Task(
  description='Gather relevant data...', 
  agent=researcher, 
  expected_output='Raw Data'
)
analysis_task = Task(
  description='Analyze the data...', 
  agent=analyst, 
  expected_output='Data Insights'
)
writing_task = Task(
  description='Compose the report...', 
  agent=writer, 
  expected_output='Final Report'
)

# 组建具有顺序流程的团队
report_crew = Crew(
  agents=[researcher, analyst, writer],
  tasks=[research_task, analysis_task, writing_task],
  process=Process.sequential
)

# 执行团队
result = report_crew.kickoff()

# 访问类型安全的输出
task_output: TaskOutput = result.tasks[0].output
crew_output: CrewOutput = result.output
```

### 注意：

顺序流程中的每个任务**必须**分配一个代理。确保每个`Task`都包含一个`agent`参数。

### 工作流程实例

1. **初始任务**：在顺序流程中，第一个代理完成其任务并发出完成信号。
2. **后续任务**：代理根据流程类型接手他们的任务，前置任务的成果或指令指导他们的执行。
3. **完成**：一旦执行最终任务，流程即告完成，导致项目完成。

## 高级功能

### 任务委托

在顺序流程中，如果代理设置了`allow_delegation`为`True`，他们可以将任务委托给团队中的其他代理。
当团队中有多个代理时，此功能会自动设置。

### 异步执行

任务可以异步执行，在适当时允许并行处理。
要创建异步任务，在定义任务时设置`async_execution=True`。

### 内存和缓存

CrewAI支持内存和缓存功能：

* **内存**：创建Crew时设置`memory=True`来启用。这允许代理在任务之间保留信息。
* **缓存**：默认情况下，缓存是启用的。设置`cache=False`可禁用它。

### 回调

您可以在任务和步骤级别设置回调：

* `task_callback`：在每个任务完成后执行。
* `step_callback`：在代理执行的每个步骤后执行。

### 使用指标

CrewAI跟踪所有任务和代理的令牌使用情况。您可以在执行后访问这些指标。

## 顺序流程的最佳实践

1. **顺序很重要**：以逻辑顺序排列任务，每个任务都建立在前一个任务的基础上。
2. **明确的任务描述**：为每个任务提供详细描述，以有效指导代理。
3. **合适的代理选择**：将代理的技能和角色与每个任务的要求相匹配。
4. **使用上下文**：利用先前任务的上下文来指导后续任务。

这份更新的文档确保细节准确反映了代码库的最新变化，并清楚地描述了如何利用新功能和配置。
内容保持简单直接，以确保易于理解。