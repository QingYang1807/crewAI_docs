# 编码代理

> 了解如何让您的 CrewAI 代理编写和执行代码，并探索增强功能的高级特性。

## 简介

CrewAI 代理现在具有编写和执行代码的强大能力，显著增强了它们解决问题的能力。此功能特别适用于需要计算或程序化解决方案的任务。

## 启用代码执行

要为代理启用代码执行，请在创建代理时将 `allow_code_execution` 参数设置为 `True`。

以下是一个示例：

```python Code theme={null}
from crewai import Agent

coding_agent = Agent(
    role="高级 Python 开发者",
    goal="精心设计和编写深思熟虑的代码",
    backstory="您是一名高级 Python 开发者，在软件架构和最佳实践方面拥有丰富经验。",
    allow_code_execution=True
)
```

<Note>
  请注意，`allow_code_execution` 参数默认为 `False`。
</Note>

## 重要注意事项

1. **模型选择**：强烈建议在启用代码执行时使用更强大的模型，如 Claude 3.5 Sonnet 和 GPT-4。
   这些模型对编程概念有更好的理解，更有可能生成正确且高效的代码。

2. **错误处理**：代码执行功能包括错误处理。如果执行的代码引发异常，代理将收到错误消息，并可以尝试更正代码或
   提供替代解决方案。`max_retry_limit` 参数默认为 2，用于控制任务的最大重试次数。

3. **依赖项**：要使用代码执行功能，您需要安装 `crewai_tools` 包。如果未安装，代理将记录一条信息消息：
   "编码工具不可用。请安装 crewai\_tools。"

## 代码执行过程

当启用了代码执行的代理遇到需要编程的任务时：

<Steps>
  <Step title="任务分析">
    代理分析任务并确定需要代码执行。
  </Step>
  <Step title="代码制定">
    它制定解决问题所需的 Python 代码。
  </Step>
  <Step title="代码执行">
    代码被发送到内部代码执行工具（`CodeInterpreterTool`）。
  </Step>
  <Step title="结果解释">
    代理解释结果并将其整合到其响应中，或用于进一步解决问题。
  </Step>
</Steps>

## 使用示例

以下是创建具有代码执行能力的代理并在任务中使用它的详细示例：

```python Code theme={null}
from crewai import Agent, Task, Crew

# 创建一个启用了代码执行的代理
coding_agent = Agent(
    role="Python 数据分析师",
    goal="使用 Python 分析数据并提供洞察",
    backstory="您是一名经验丰富的数据分析师，具备强大的 Python 技能。",
    allow_code_execution=True
)

# 创建一个需要代码执行的任务
data_analysis_task = Task(
    description="分析给定的数据集并计算参与者的平均年龄。",
    agent=coding_agent
)

# 创建一个团队并添加任务
analysis_crew = Crew(
    agents=[coding_agent],
    tasks=[data_analysis_task]
)

# 执行团队
result = analysis_crew.kickoff()

print(result)
```

在此示例中，`coding_agent` 可以编写和执行 Python 代码来执行数据分析任务。