# MultiOn 工具

> `MultiOnTool` 赋予 CrewAI 智能体通过自然语言指令浏览和交互网页的能力。

## 概述

`MultiOnTool` 旨在封装 [MultiOn](https://docs.multion.ai/welcome) 的网页浏览功能，使 CrewAI 智能体能够使用自然语言指令控制网页浏览器。该工具实现了无缝的网页浏览，使其成为需要动态网页数据交互和自动化网页任务的项目中的重要资产。

## 安装

要使用此工具，您需要安装 MultiOn 包：

```shell
uv add multion
```

您还需要安装 MultiOn 浏览器扩展并启用 API 使用功能。

## 开始步骤

要有效使用 `MultiOnTool`，请按照以下步骤操作：

1. **安装 CrewAI**：确保在您的 Python 环境中安装了 `crewai[tools]` 包。
2. **安装并使用 MultiOn**：按照 [MultiOn 文档](https://docs.multion.ai/learn/browser-extension) 安装 MultiOn 浏览器扩展。
3. **启用 API 使用功能**：在浏览器扩展文件夹中点击 MultiOn 扩展（而不是网页上的 MultiOn 悬浮图标）打开扩展配置。点击 API Enabled 开关以启用 API。

## 示例

以下示例演示了如何初始化工具并执行网页浏览任务：

```python
from crewai import Agent, Task, Crew
from crewai_tools import MultiOnTool

# 初始化工具
multion_tool = MultiOnTool(api_key="YOUR_MULTION_API_KEY", local=False)

# 定义使用该工具的智能体
browser_agent = Agent(
    role="浏览器智能体",
    goal="使用自然语言控制网页浏览器",
    backstory="一个专业的浏览智能体。",
    tools=[multion_tool],
    verbose=True,
)

# 搜索和总结新闻的示例任务
browse_task = Task(
    description="总结前3条热门AI新闻标题",
    expected_output="前3条热门AI新闻标题的总结",
    agent=browser_agent,
)

# 创建并运行团队
crew = Crew(agents=[browser_agent], tasks=[browse_task])
result = crew.kickoff()
```

## 参数

`MultiOnTool` 在初始化时接受以下参数：

* **api_key**：可选。指定 MultiOn API 密钥。如果未提供，它将查找 `MULTION_API_KEY` 环境变量。
* **local**：可选。设置为 `True` 以在本地浏览器上运行智能体。确保已安装 MultiOn 浏览器扩展并勾选了 API Enabled。默认为 `False`。
* **max_steps**：可选。设置 MultiOn 智能体为执行一个命令可以采取的最大步骤数。默认为 `3`。

## 使用方法

使用 `MultiOnTool` 时，智能体将提供自然语言指令，该工具会将这些指令转换为网页浏览操作。工具返回浏览会话的结果以及状态。

```python
# 与智能体一起使用工具的示例
browser_agent = Agent(
    role="网页浏览器智能体",
    goal="从网上搜索并总结信息",
    backstory="一个擅长从网站查找和提取信息的专家。",
    tools=[multion_tool],
    verbose=True,
)

# 为智能体创建任务
search_task = Task(
    description="在 TechCrunch 上搜索最新的AI新闻并总结前3条标题",
    expected_output="来自 TechCrunch 的前3条AI新闻标题的总结",
    agent=browser_agent,
)

# 运行任务
crew = Crew(agents=[browser_agent], tasks=[search_task])
result = crew.kickoff()
```

如果返回的状态是 `CONTINUE`，则应指示智能体重新发出相同的指令以继续执行。

## 实现细节

`MultiOnTool` 实现为 CrewAI 的 `BaseTool` 的子类。它封装了 MultiOn 客户端以提供网页浏览功能：

```python
class MultiOnTool(BaseTool):
    """封装 MultiOn 浏览功能的工具。"""

    name: str = "Multion 浏览工具"
    description: str = """Multion 使大型语言模型能够使用自然语言指令控制网页浏览器。
            如果状态是 'CONTINUE'，请重新发出相同指令以继续执行
        """
  
    # 实现细节...
  
    def _run(self, cmd: str, *args: Any, **kwargs: Any) -> str:
        """
        使用给定命令运行 Multion 客户端。
      
        Args:
            cmd (str): 用于网页浏览的详细和具体的自然语言指令
            *args (Any): 传递给 Multion 客户端的额外参数
            **kwargs (Any): 传递给 Multion 客户端的额外关键字参数
        """
        # 实现细节...
```

## 结论

`MultiOnTool` 为将网页浏览功能集成到 CrewAI 智能体中提供了强大的方法。通过使智能体能够通过自然语言指令与网站交互，它为基于网页的任务开辟了广泛的可能性，从数据收集和研究到与网页服务的自动化交互。