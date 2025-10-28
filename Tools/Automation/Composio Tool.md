# Composio 工具

> Composio 为 AI 智能体提供 250 多个生产就绪的工具，并具备灵活的身份验证管理功能。

# `ComposioToolSet`

## 描述

Composio 是一个集成平台，允许您将 AI 智能体连接到 250 多个工具。主要功能包括：

* **企业级身份验证**：内置支持 OAuth、API 密钥、JWT，并具有自动令牌刷新功能
* **全面可观测性**：详细的工具使用日志、执行时间戳等

## 安装

要将 Composio 工具集成到您的项目中，请按照以下说明操作：

```shell theme={null}
pip install composio-crewai
pip install crewai
```

安装完成后，运行 `composio login` 或将您的 Composio API 密钥导出为 `COMPOSIO_API_KEY`。从[此处](https://app.composio.dev)获取您的 Composio API 密钥。

## 示例

以下示例演示了如何初始化工具并执行 GitHub 操作：

1. 初始化 Composio 工具集

```python Code theme={null}
from composio_crewai import ComposioToolSet, App, Action
from crewai import Agent, Task, Crew

toolset = ComposioToolSet()
```

2. 连接您的 GitHub 账户

<CodeGroup>
  ```shell CLI theme={null}
  composio add github
  ```

  ```python Code theme={null}
  request = toolset.initiate_connection(app=App.GITHUB)
  print(f"打开此 URL 进行身份验证：{request.redirectUrl}")
  ```
</CodeGroup>

3. 获取工具

* 从应用程序获取所有工具（不推荐用于生产环境）：

```python Code theme={null}
tools = toolset.get_tools(apps=[App.GITHUB])
```

* 根据标签过滤工具：

```python Code theme={null}
tag = "users"

filtered_action_enums = toolset.find_actions_by_tags(
    App.GITHUB,
    tags=[tag], 
)

tools = toolset.get_tools(actions=filtered_action_enums)
```

* 根据用例过滤工具：

```python Code theme={null}
use_case = "在 GitHub 上为仓库加星标"

filtered_action_enums = toolset.find_actions_by_use_case(
    App.GITHUB, use_case=use_case, advanced=False
)

tools = toolset.get_tools(actions=filtered_action_enums)
```

<Tip>将 `advanced` 设置为 True 以获取复杂用例的操作</Tip>

* 使用特定工具：

在本演示中，我们将使用来自 GitHub 应用的 `GITHUB_STAR_A_REPOSITORY_FOR_THE_AUTHENTICATED_USER` 操作。

```python Code theme={null}
tools = toolset.get_tools(
    actions=[Action.GITHUB_STAR_A_REPOSITORY_FOR_THE_AUTHENTICATED_USER]
)
```

了解更多关于过滤操作的信息[请点击此处](https://docs.composio.dev/patterns/tools/use-tools/use-specific-actions)

4. 定义智能体

```python Code theme={null}
crewai_agent = Agent(
    role="GitHub 智能体",
    goal="您使用 GitHub API 在 GitHub 上执行操作",
    backstory="您是一个 AI 智能体，负责代表用户使用 GitHub API 在 GitHub 上执行操作",
    verbose=True,
    tools=tools,
    llm= # 传入一个 llm
)
```

5. 执行任务

```python Code theme={null}
task = Task(
    description="在 GitHub 上为 composiohq/composio 仓库加星标",
    agent=crewai_agent,
    expected_output="操作状态",
)

crew = Crew(agents=[crewai_agent], tasks=[task])

crew.kickoff()
```

* 更详细的工具列表可以在[此处](https://app.composio.dev)找到