# 条件任务

> 了解如何在 crewAI 启动流程中使用条件任务

## 简介

crewAI 中的条件任务允许根据先前任务的结果动态调整工作流程。
这一强大功能使 Crew 能够做出决策并选择性地执行任务，从而提高 AI 驱动流程的灵活性和效率。

## 使用示例

```python Code theme={null}
from typing import List
from pydantic import BaseModel
from crewai import Agent, Crew
from crewai.tasks.conditional_task import ConditionalTask
from crewai.tasks.task_output import TaskOutput
from crewai.task import Task
from crewai_tools import SerperDevTool

# 为条件任务定义一个条件函数
# 如果结果为 false，则跳过该任务；如果为 true，则执行该任务。
def is_data_missing(output: TaskOutput) -> bool:
    return len(output.pydantic.events) < 10  # 如果事件少于10个，将跳过此任务

# 定义智能体
data_fetcher_agent = Agent(
    role="数据获取者",
    goal="使用 Serper 工具在线获取数据",
    backstory="背景故事1",
    verbose=True,
    tools=[SerperDevTool()]
)

data_processor_agent = Agent(
    role="数据处理者",
    goal="处理获取的数据",
    backstory="背景故事2",
    verbose=True
)

summary_generator_agent = Agent(
    role="摘要生成器",
    goal="根据获取的数据生成摘要",
    backstory="背景故事3",
    verbose=True
)

class EventOutput(BaseModel):
    events: List[str]

task1 = Task(
    description="使用 Serper 工具获取关于旧金山活动的数据",
    expected_output="本周在旧金山可做的10件事清单",
    agent=data_fetcher_agent,
    output_pydantic=EventOutput,
)

conditional_task = ConditionalTask(
    description="""
        检查数据是否缺失。如果我们有不到10个活动，
        使用 Serper 工具获取更多活动，以确保
        本周旧金山共有10个活动。
        """,
    expected_output="本周在旧金山可做的10件事清单",
    condition=is_data_missing,
    agent=data_processor_agent,
)

task3 = Task(
    description="根据获取的数据生成旧金山活动摘要",
    expected_output="关于客户及其客户和竞争对手的完整报告，包括他们的人口统计、偏好、市场定位和受众参与度。",
    agent=summary_generator_agent,
)

# 使用任务创建 Crew
crew = Crew(
    agents=[data_fetcher_agent, data_processor_agent, summary_generator_agent],
    tasks=[task1, conditional_task, task3],
    verbose=True,
    planning=True
)

# 运行 Crew
result = crew.kickoff()
print("结果", result)
```