# 将工具输出强制作为结果

> 了解如何在 CrewAI 中将工具输出强制作为 Agent 任务的结果。

## 简介

在 CrewAI 中，您可以将工具的输出强制作为 agent 任务的结果。
当您希望确保工具输出被捕获并作为任务结果返回，避免在任务执行过程中被 agent 修改时，此功能非常有用。

## 将工具输出强制作为结果

要将工具输出强制作为 agent 任务的结果，您需要在向 agent 添加工具时将 `result_as_answer` 参数设置为 `True`。
该参数确保工具输出被捕获并作为任务结果返回，而不经过 agent 的任何修改。

以下是将工具输出强制作为 agent 任务结果的示例：

```python Code theme={null}
from crewai.agent import Agent
from my_tool import MyCustomTool

# 使用自定义工具创建一个编码 agent
coding_agent = Agent(
        role="数据科学家",
        goal="生成关于 AI 的精彩报告",
        backstory="您处理数据和 AI 工作",
        tools=[MyCustomTool(result_as_answer=True)],
    )

# 假设工具的执行和结果填充在系统内部进行
task_result = coding_agent.execute_task(task)
```

## 工作流程演示

<Steps>
  <Step title="任务执行">
    Agent 使用提供的工具执行任务。
  </Step>

  <Step title="工具输出">
    工具生成输出，该输出被捕获为任务结果。
  </Step>

  <Step title="Agent 交互">
    Agent 可以从工具中反思和学习，但输出不会被修改。
  </Step>

  <Step title="结果返回">
    工具输出未经任何修改直接作为任务结果返回。
  </Step>
</Steps>