# 为列表中的每一项启动团队

> 为列表中的每一项启动团队

## 简介

CrewAI 提供了为列表中每一项启动团队的能力，允许您对列表中的每一项执行该团队。
当您需要为多个项目执行相同的任务集时，此功能特别有用。

## 为每一项启动团队

要为列表中的每一项启动团队，请使用 `kickoff_for_each()` 方法。
此方法会为列表中的每一项执行团队，使您能够高效地处理多个项目。

以下是为列表中的每一项启动团队的示例：

```python 代码主题={null}
from crewai import Crew, Agent, Task

# 创建一个启用代码执行的代理
coding_agent = Agent(
    role="Python数据分析师",
    goal="使用Python分析数据并提供洞察",
    backstory="您是一名具有强大Python技能的资深数据分析师。",
    allow_code_execution=True
)

# 创建一个需要代码执行的任务
data_analysis_task = Task(
    description="分析给定的数据集并计算参与者的平均年龄。年龄：{ages}",
    agent=coding_agent,
    expected_output="从数据集计算得出的平均年龄"
)

# 创建一个团队并添加任务
analysis_crew = Crew(
    agents=[coding_agent],
    tasks=[data_analysis_task],
    verbose=True,
    memory=False
)

datasets = [
  { "ages": [25, 30, 35, 40, 45] },
  { "ages": [20, 25, 30, 35, 40] },
  { "ages": [30, 35, 40, 45, 50] }
]

# 执行团队
result = analysis_crew.kickoff_for_each(inputs=datasets)
```