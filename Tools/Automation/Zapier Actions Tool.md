# Zapier 操作工具

> `ZapierActionsAdapter` 将 Zapier 操作作为 CrewAI 工具公开，以实现自动化。

# `ZapierActionsAdapter`

## 描述

使用 Zapier 适配器将 Zapier 操作作为 CrewAI 工具进行列表查看和调用。这使代理能够触发数千个应用程序中的自动化操作。

## 安装

此适配器包含在 `crewai-tools` 中。无需额外安装。

## 环境变量

* `ZAPIER_API_KEY`（必需）：Zapier API 密钥。从 Zapier 操作仪表板 [https://actions.zapier.com/](https://actions.zapier.com/) 获取（创建账户，然后生成 API 密钥）。您也可以在构造适配器时直接传递 `zapier_api_key`。

## 示例

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools.adapters.zapier_adapter import ZapierActionsAdapter

adapter = ZapierActionsAdapter(api_key="your_zapier_api_key")
tools = adapter.tools()

agent = Agent(
    role="Automator",
    goal="Execute Zapier actions",
    backstory="Automation specialist",
    tools=tools,
    verbose=True,
)

task = Task(
    description="Create a new Google Sheet and add a row using Zapier actions",
    expected_output="Confirmation with created resource IDs",
    agent=agent,
)

crew = Crew(agents=[agent], tasks=[task])
result = crew.kickoff()
```

## 注意事项与限制

* 适配器列出您的密钥的可用操作，并动态创建 `BaseTool` 包装器。
* 在任务指令或工具调用中处理操作特定的必需字段。
* 速率限制取决于您的 Zapier 计划；请参阅 Zapier 操作文档。

## 注意事项

* 适配器获取可用操作并动态生成 `BaseTool` 包装器。