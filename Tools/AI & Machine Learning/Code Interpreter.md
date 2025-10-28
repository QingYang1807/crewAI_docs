# 代码解释器

> `CodeInterpreterTool` 是一个强大的工具，旨在安全、隔离的环境中执行 Python 3 代码。

# `CodeInterpreterTool`

## 描述

`CodeInterpreterTool` 使 CrewAI 代理能够执行他们自主生成的 Python 3 代码。此功能特别有价值，因为它允许代理创建代码、执行代码、获取结果，并利用这些信息来指导后续的决策和行动。

有几种使用此工具的方式：

### Docker 容器（推荐）

这是主要选项。代码在安全的、隔离的 Docker 容器中运行，无论其内容如何，都能确保安全性。
确保您的系统上已安装并运行 Docker。如果没有，您可以从[此处](https://docs.docker.com/get-docker/)安装。

### 沙箱环境

如果 Docker 不可用——无论是因为未安装还是因任何原因无法访问——代码将在受限制的 Python 环境中执行，称为沙箱。
这个环境非常有限，对许多模块和内置函数有严格的限制。

### 不安全执行

**不推荐用于生产环境**
此模式允许执行任何 Python 代码，包括对 `sys, os..` 和类似模块的危险调用。[查看](/en/tools/ai-ml/codeinterpretertool#enabling-unsafe-mode)如何启用此模式

## 日志记录

`CodeInterpreterTool` 将所选的执行策略记录到 STDOUT

## 安装

要使用此工具，您需要安装 CrewAI 工具包：

```shell  theme={null}
pip install 'crewai[tools]'
```

## 示例

以下示例演示了如何将 `CodeInterpreterTool` 与 CrewAI 代理一起使用：

```python Code theme={null}
from crewai import Agent, Task, Crew, Process
from crewai_tools import CodeInterpreterTool

# 初始化工具
code_interpreter = CodeInterpreterTool()

# 定义使用该工具的代理
programmer_agent = Agent(
    role="Python 程序员",
    goal="编写并执行 Python 代码来解决问题",
    backstory="一名专业的 Python 程序员，能够编写高效的代码来解决复杂问题。",
    tools=[code_interpreter],
    verbose=True,
)

# 生成并执行代码的示例任务
coding_task = Task(
    description="编写一个 Python 函数来计算斐波那契数列到第 10 个数字并打印结果。",
    expected_output="到第 10 个数字的斐波那契数列。",
    agent=programmer_agent,
)

# 创建并运行团队
crew = Crew(
    agents=[programmer_agent],
    tasks=[coding_task],
    verbose=True,
    process=Process.sequential,
)
result = crew.kickoff()
```

您还可以在创建代理时直接启用代码执行：

```python Code theme={null}
from crewai import Agent

# 创建启用了代码执行的代理
programmer_agent = Agent(
    role="Python 程序员",
    goal="编写并执行 Python 代码来解决问题",
    backstory="一名专业的 Python 程序员，能够编写高效的代码来解决复杂问题。",
    allow_code_execution=True,  # 这会自动添加 CodeInterpreterTool
    verbose=True,
)
```

### 启用 `unsafe_mode`

```python Code theme={null}
from crewai_tools import CodeInterpreterTool

code = """
import os
os.system("ls -la")
"""

CodeInterpreterTool(unsafe_mode=True).run(code=code)
```

## 参数

`CodeInterpreterTool` 在初始化时接受以下参数：

* **user\_dockerfile\_path**：可选。用于代码解释器容器的自定义 Dockerfile 路径。
* **user\_docker\_base\_url**：可选。用于运行容器的 Docker 守护进程的 URL。
* **unsafe\_mode**：可选。是否直接在主机机上运行代码，而不是在 Docker 容器或沙箱中。默认为 `False`。请谨慎使用！
* **default\_image\_tag**：可选。默认 Docker 镜像标签。默认为 `code-interpreter:latest`

当与代理一起使用该工具时，代理需要提供：

* **code**：必需。要执行的 Python 3 代码。
* **libraries\_used**：可选。代码中使用的需要安装的库列表。默认为 `[]`

## 代理集成示例

这是一个更详细的示例，展示如何将 `CodeInterpreterTool` 与 CrewAI 代理集成：

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import CodeInterpreterTool

# 初始化工具
code_interpreter = CodeInterpreterTool()

# 定义使用该工具的代理
data_analyst = Agent(
    role="数据分析师",
    goal="使用 Python 代码分析数据",
    backstory="""你是一名专业的数据分析师，专门使用 Python
    来分析和可视化数据。你可以编写高效的代码来处理
    大型数据集并提取有意义的见解。""",
    tools=[code_interpreter],
    verbose=True,
)

# 为代理创建任务
analysis_task = Task(
    description="""
    编写 Python 代码来：
    1. 生成一个包含 x 和 y 坐标的 100 个点的随机数据集
    2. 计算 x 和 y 之间的相关系数
    3. 创建数据的散点图
    4. 打印相关系数并将图表保存为 'scatter.png'

    确保处理任何必要的导入并打印结果。
    """,
    expected_output="相关系数以及确认散点图已保存的信息。",
    agent=data_analyst,
)

# 运行任务
crew = Crew(
    agents=[data_analyst],
    tasks=[analysis_task],
    verbose=True,
    process=Process.sequential,
)
result = crew.kickoff()
```

## 实现细节

`CodeInterpreterTool` 使用 Docker 为代码执行创建安全环境：

```python Code theme={null}
class CodeInterpreterTool(BaseTool):
    name: str = "代码解释器"
    description: str = "解释带有最终打印语句的 Python3 代码字符串。"
    args_schema: Type[BaseModel] = CodeInterpreterSchema
    default_image_tag: str = "code-interpreter:latest"

    def _run(self, **kwargs) -> str:
        code = kwargs.get("code", self.code)
        libraries_used = kwargs.get("libraries_used", [])

        if self.unsafe_mode:
            return self.run_code_unsafe(code, libraries_used)
        else:
            return self.run_code_safety(code, libraries_used)
```

该工具执行以下步骤：

1. 验证 Docker 镜像是否存在，如果需要则构建它
2. 创建一个挂载了当前工作目录的 Docker 容器
3. 安装代理指定的任何必需库
4. 在容器中执行 Python 代码
5. 返回代码执行的输出
6. 通过停止并删除容器来清理

## 安全考虑

默认情况下，`CodeInterpreterTool` 在隔离的 Docker 容器中运行代码，这提供了一层安全性。但是，仍有一些安全注意事项需要牢记：

1. Docker 容器可以访问当前工作目录，因此可能访问敏感文件。
2. 如果 Docker 容器不可用且代码需要在安全环境下运行，它将在沙箱环境中执行。出于安全原因，不允许安装任意库。
3. `unsafe_mode` 参数允许代码直接在主机机上执行，这只能在受信任的环境中使用。
4. 在允许代理安装任意库时要小心，因为它们可能包含恶意代码。

## 结论

`CodeInterpreterTool` 为 CrewAI 代理在相对安全的环境中执行 Python 代码提供了强大的方式。通过使代理能够编写和运行代码，它显著扩展了他们解决问题的能力，特别是对于涉及数据分析、计算或其他计算工作的任务。此工具对于需要执行复杂操作的代理特别有用，这些操作用代码表达比用自然语言更有效。