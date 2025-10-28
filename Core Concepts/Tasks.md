# 任务

> 关于在 CrewAI 框架内管理和创建任务的详细指南。

## 概述

在 CrewAI 框架中，`任务`是由`代理`完成的具体分配。

任务提供了执行所需的所有详细信息，如描述、负责的代理、所需工具等，从而支持各种复杂程度的操作。

CrewAI 内的任务可以是协作的，需要多个代理一起工作。这是通过任务属性管理的，并由团队的过程协调，以增强团队合作和效率。

<Note type="info" title="企业增强功能：可视化任务构建器">
  CrewAI AMP 在 Crew Studio 中包含一个可视化任务构建器，可简化复杂任务的创建和链接。可视化设计您的任务流程并实时测试，无需编写代码。

  <img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=c4f5428b111816273b3b53d9cef14fad" alt="任务构建器截图" data-og-width="2654" width="2654" data-og-height="1710" height="1710" data-path="images/enterprise/crew-studio-interface.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=35ea9140f0b9e57da5f45adbc7e2f166 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=ae6f0c18ef3679b5466177710fbc4a94 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=6c3e2fe013ab4826da90c937a9855635 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=7f1474dd7f983532dc910363b96f783a 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=f1a6d7e744e6862af5e72dce4deb0fd1 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=74aeb1ccd8e2c8f84d4247b8d0259737 2500w" />

  可视化任务构建器支持：

  * 拖放式任务创建
  * 可视化任务依赖关系和流程
  * 实时测试和验证
  * 轻松共享和协作
</Note>

### 任务执行流程

任务可以两种方式执行：

* **顺序执行**：按照定义的顺序执行任务
* **分层执行**：根据代理的角色和专业分配任务

执行流程在创建团队时定义：

```python Code theme={null}
crew = Crew(
    agents=[agent1, agent2],
    tasks=[task1, task2],
    process=Process.sequential  # 或 Process.hierarchical
)
```

## 任务属性

| 属性 | 参数 | 类型 | 描述 |
| :--- | :--- | :--- | :--- |
| **描述** | `description` | `str` | 对任务内容的清晰简洁陈述。 |
| **预期输出** | `expected_output` | `str` | 对任务完成状态的详细描述。 |
| **名称** *(可选)* | `name` | `Optional[str]` | 任务的名称标识符。 |
| **代理** *(可选)* | `agent` | `Optional[BaseAgent]` | 负责执行任务的代理。 |
| **工具** *(可选)* | `tools` | `List[BaseTool]` | 代理在此任务中仅限于使用的工具/资源。 |
| **上下文** *(可选)* | `context` | `Optional[List["Task"]]` | 其输出将用作此任务上下文的其他任务。 |
| **异步执行** *(可选)* | `async_execution` | `Optional[bool]` | 任务是否应异步执行。默认为 False。 |
| **人工输入** *(可选)* | `human_input` | `Optional[bool]` | 任务是否应有人工审查代理的最终答案。默认为 False。 |
| **Markdown** *(可选)* | `markdown` | `Optional[bool]` | 任务是否应指示代理返回 Markdown 格式的最终答案。默认为 False。 |
| **配置** *(可选)* | `config` | `Optional[Dict[str, Any]]` | 任务特定的配置参数。 |
| **输出文件** *(可选)* | `output_file` | `Optional[str]` | 用于存储任务输出的文件路径。 |
| **创建目录** *(可选)* | `create_directory` | `Optional[bool]` | 如果 output\_file 的目录不存在，是否创建它。默认为 True。 |
| **输出 JSON** *(可选)* | `output_json` | `Optional[Type[BaseModel]]` | 用于构造 JSON 输出的 Pydantic 模型。 |
| **输出 Pydantic** *(可选)* | `output_pydantic` | `Optional[Type[BaseModel]]` | 用于任务输出的 Pydantic 模型。 |
| **回调** *(可选)* | `callback` | `Optional[Any]` | 任务完成后执行的函数/对象。 |
| **护栏** *(可选)* | `guardrail` | `Optional[Callable]` | 在进入下一个任务之前验证任务输出的函数。 |
| **护栏最大重试次数** *(可选)* | `guardrail_max_retries` | `Optional[int]` | 护栏验证失败时的最大重试次数。默认为 3。 |

<Note type="warning" title="已弃用：max_retries">
  任务属性 `max_retries` 已弃用，将在 v1.0.0 中移除。
  请改用 `guardrail_max_retries` 来控制护栏失败时的重试尝试。
</Note>

## 创建任务

在 CrewAI 中有两种创建任务的方法：使用 **YAML 配置（推荐）** 或**直接在代码中定义**。

### YAML 配置（推荐）

使用 YAML 配置提供了一种更清晰、更易维护的方式来定义任务。我们强烈建议在 CrewAI 项目中使用这种方法来定义任务。

按照[安装](/en/installation)部分中的说明创建 CrewAI 项目后，导航到 `src/latest_ai_development/config/tasks.yaml` 文件，并修改模板以符合您的特定任务要求。

<Note>
  YAML 文件中的变量（如 `{topic}`）将在运行团队时替换为您的输入值：

  ```python Code theme={null}
  crew.kickoff(inputs={'topic': 'AI 代理'})
  ```
</Note>

以下是使用 YAML 配置任务的示例：

````yaml tasks.yaml theme={null}
research_task:
  description: >
    对 {topic} 进行彻底研究
    确保你找到任何有趣且相关的信息，
    考虑到当前年份是 2025 年。
  expected_output: >
    包含 10 个关于 {topic} 最相关信息的要点列表
  agent: researcher

reporting_task:
  description: >
    审查你获得的上下文，并将每个主题扩展为报告的完整部分。
    确保报告详细，包含所有相关信息。
  expected_output: >
    一份完整的报告，包含主要主题，每个主题都有完整的信息部分。
    格式为 markdown，不包含 '```'
  agent: reporting_analyst
  markdown: true
  output_file: report.md
````

要在您的代码中使用此 YAML 配置，创建一个继承自 `CrewBase` 的团队类：

```python crew.py theme={null}
# src/latest_ai_development/crew.py

from crewai import Agent, Crew, Process, Task
from crewai.project import CrewBase, agent, crew, task
from crewai_tools import SerperDevTool

@CrewBase
class LatestAiDevelopmentCrew():
  """LatestAiDevelopment crew"""

  @agent
  def researcher(self) -> Agent:
    return Agent(
      config=self.agents_config['researcher'], # type: ignore[index]
      verbose=True,
      tools=[SerperDevTool()]
    )

  @agent
  def reporting_analyst(self) -> Agent:
    return Agent(
      config=self.agents_config['reporting_analyst'], # type: ignore[index]
      verbose=True
    )

  @task
  def research_task(self) -> Task:
    return Task(
      config=self.tasks_config['research_task'] # type: ignore[index]
    )

  @task
  def reporting_task(self) -> Task:
    return Task(
      config=self.tasks_config['reporting_task'] # type: ignore[index]
    )

  @crew
  def crew(self) -> Crew:
    return Crew(
      agents=[
        self.researcher(),
        self.reporting_analyst()
      ],
      tasks=[
        self.research_task(),
        self.reporting_task()
      ],
      process=Process.sequential
    )
```

<Note>
  您在 YAML 文件（`agents.yaml` 和 `tasks.yaml`）中使用的名称应与 Python 代码中的方法名称匹配。
</Note>

### 直接代码定义（替代方法）

或者，您可以直接在代码中定义任务，而不使用 YAML 配置：

```python task.py theme={null}
from crewai import Task

research_task = Task(
    description="""
        对 AI 代理进行彻底研究。
        确保你找到任何有趣且相关的信息，
        考虑到当前年份是 2025 年。
    """,
    expected_output="""
        包含 10 个关于 AI 代理最相关信息的要点列表
    """,
    agent=researcher
)

reporting_task = Task(
    description="""
        审查你获得的上下文，并将每个主题扩展为报告的完整部分。
        确保报告详细，包含所有相关信息。
    """,
    expected_output="""
        一份完整的报告，包含主要主题，每个主题都有完整的信息部分。
    """,
    agent=reporting_analyst,
    markdown=True,  # 为最终输出启用 markdown 格式
    output_file="report.md"
)
```

<Tip>
  直接指定 `agent` 进行分配，或者让 `hierarchical` CrewAI 的流程根据角色、可用性等决定。
</Tip>

## 任务输出

理解任务输出对于构建有效的 AI 工作流程至关重要。CrewAI 通过 `TaskOutput` 类提供了处理任务结果的结构化方法，该方法支持多种输出格式，并且可以在任务之间轻松传递。

在 CrewAI 框架中，任务的输出被封装在 `TaskOutput` 类中。此类提供了访问任务结果的结构化方式，包括各种格式，如原始输出、JSON 和 Pydantic 模型。

默认情况下，`TaskOutput` 将仅包含 `raw` 输出。只有当原始 `Task` 对象配置了 `output_pydantic` 或 `output_json` 时，`TaskOutput` 才会包含 `pydantic` 或 `json_dict` 输出。

### 任务输出属性

| 属性 | 参数 | 类型 | 描述 |
| :--- | :--- | :--- | :--- |
| **描述** | `description` | `str` | 任务的描述。 |
| **摘要** | `summary` | `Optional[str]` | 任务的摘要，从描述的前 10 个词自动生成。 |
| **原始输出** | `raw` | `str` | 任务的原始输出。这是输出的默认格式。 |
| **Pydantic** | `pydantic` | `Optional[BaseModel]` | 表示任务结构化输出的 Pydantic 模型对象。 |
| **JSON 字典** | `json_dict` | `Optional[Dict[str, Any]]` | 表示任务 JSON 输出的字典。 |
| **代理** | `agent` | `str` | 执行任务的代理。 |
| **输出格式** | `output_format` | `OutputFormat` | 任务输出的格式，选项包括 RAW、JSON 和 Pydantic。默认为 RAW。 |

### 任务方法和属性

| 方法/属性 | 描述 |
| :--- | :--- |
| **json** | 如果输出格式为 JSON，则返回任务输出的 JSON 字符串表示。 |
| **to\_dict** | 将 JSON 和 Pydantic 输出转换为字典。 |
| **str** | 返回任务输出的字符串表示，优先考虑 Pydantic，然后是 JSON，最后是原始输出。 |

### 访问任务输出

任务执行后，可以通过 `Task` 对象的 `output` 属性访问其输出。`TaskOutput` 类提供了多种与输出交互和呈现的方式。

#### 示例

```python Code theme={null}
# 示例任务
task = Task(
    description='查找并总结最新的 AI 新闻',
    expected_output='前 5 条最重要的 AI 新闻的要点列表摘要',
    agent=research_agent,
    tools=[search_tool]
)

# 执行团队
crew = Crew(
    agents=[research_agent],
    tasks=[task],
    verbose=True
)

result = crew.kickoff()

# 访问任务输出
task_output = task.output

print(f"任务描述: {task_output.description}")
print(f"任务摘要: {task_output.summary}")
print(f"原始输出: {task_output.raw}")
if task_output.json_dict:
    print(f"JSON 输出: {json.dumps(task_output.json_dict, indent=2)}")
if task_output.pydantic:
    print(f"Pydantic 输出: {task_output.pydantic}")
```

## Markdown 输出格式化

`markdown` 参数启用任务输出的自动 markdown 格式化。设置为 `True` 时，任务将指示代理使用适当的 Markdown 语法格式化最终答案。

### 使用 Markdown 格式化

```python Code theme={null}
# 启用 markdown 格式化的示例任务
formatted_task = Task(
    description="创建关于 AI 趋势的综合报告",
    expected_output="包含标题、章节和要点的结构良好的报告",
    agent=reporter_agent,
    markdown=True  # 启用自动 markdown 格式化
)
```

当 `markdown=True` 时，代理将收到额外的指示，使用以下格式化输出：

* `#` 用于标题
* `**文本**` 用于粗体文本
* `*文本*` 用于斜体文本
* `-` 或 `*` 用于要点
* `` `代码` `` 用于内联代码
* `语言 \`\`\` 用于代码块

### 带 Markdown 的 YAML 配置

```yaml tasks.yaml theme={null}
analysis_task:
  description: >
    分析市场数据并创建详细报告
  expected_output: >
    包含图表和关键发现的分析
  agent: analyst
  markdown: true  # 启用 markdown 格式化
  output_file: analysis.md
```

### Markdown 输出的优势

* **一致的格式化**：确保所有输出都遵循适当的 markdown 约定
* **更好的可读性**：带有标题、列表和强调的结构化内容
* **文档就绪**：输出可以直接在文档系统中使用
* **跨平台兼容性**：Markdown 得到普遍支持

<Note>
  当 `markdown=True` 时，markdown 格式化指令会自动添加到任务提示中，因此您无需在任务描述中指定格式化要求。
</Note>

## 任务依赖和上下文

任务可以使用 `context` 属性依赖于其他任务的输出。例如：

```python Code theme={null}
research_task = Task(
    description="研究 AI 的最新发展",
    expected_output="最近 AI 发展的列表",
    agent=researcher
)

analysis_task = Task(
    description="分析研究结果并识别关键趋势",
    expected_output="AI 趋势的分析报告",
    agent=analyst,
    context=[research_task]  # 此任务将等待 research_task 完成
)
```

## 任务护栏

任务护栏提供了一种在任务输出传递到下一个任务之前验证和转换输出的方法。此功能有助于确保数据质量，并在代理的输出不符合特定标准时提供反馈。

护栏是作为包含自定义验证逻辑的 Python 函数实现的，让您完全控制验证过程，并确保可靠、确定的结果。

### 基于函数的护栏

要通过 `guardrail` 参数为任务添加基于函数的护栏：

```python Code theme={null}
from typing import Tuple, Union, Dict, Any
from crewai import TaskOutput

def validate_blog_content(result: TaskOutput) -> Tuple[bool, Any]:
    """验证博客内容是否符合要求。"""
    try:
        # 检查字数
        word_count = len(result.split())
        if word_count > 200:
            return (False, "博客内容超过 200 字")

        # 此处添加其他验证逻辑
        return (True, result.strip())
    except Exception as e:
        return (False, "验证过程中出现意外错误")

blog_task = Task(
    description="写一篇关于 AI 的博客文章",
    expected_output="一篇 200 字以内的博客文章",
    agent=blog_agent,
    guardrail=validate_blog_content  # 添加护栏函数
)
```

### 护栏函数要求

1. **函数签名**：
   * 必须接受一个参数（任务输出）
   * 应返回 `(bool, Any)` 类型的元组
   * 类型提示是推荐的但可选的

2. **返回值**：
   * 成功时：返回 `(bool, Any)` 类型的元组。例如：`(True, validated_result)`
   * 失败时：返回 `(bool, str)` 类型的元组。例如：`(False, "解释失败的错误消息")`

### 错误处理最佳实践

1. **结构化错误响应**：

```python Code theme={null}
from crewai import TaskOutput, LLMGuardrail

def validate_with_context(result: TaskOutput) -> Tuple[bool, Any]:
    try:
        # 主要验证逻辑
        validated_data = perform_validation(result)
        return (True, validated_data)
    except ValidationError as e:
        return (False, f"VALIDATION_ERROR: {str(e)}")
    except Exception as e:
        return (False, str(e))
```

2. **错误类别**：
   * 使用特定的错误代码
   * 包含相关上下文
   * 提供可操作的反馈

3. **验证链**：

```python Code theme={null}
from typing import Any, Dict, List, Tuple, Union
from crewai import TaskOutput

def complex_validation(result: TaskOutput) -> Tuple[bool, Any]:
    """链接多个验证步骤。"""
    # 第 1 步：基本验证
    if not result:
        return (False, "空结果")

    # 第 2 步：内容验证
    try:
        validated = validate_content(result)
        if not validated:
            return (False, "无效内容")

        # 第 3 步：格式验证
        formatted = format_output(validated)
        return (True, formatted)
    except Exception as e:
        return (False, str(e))
```

### 处理护栏结果

当护栏返回 `(False, error)` 时：

1. 错误会发送回给代理
2. 代理尝试修复问题
3. 过程重复直到：
   * 护栏返回 `(True, result)`
   * 达到最大重试次数（`guardrail_max_retries`）

包含重试处理的示例：

```python Code theme={null}
from typing import Optional, Tuple, Union
from crewai import TaskOutput, Task

def validate_json_output(result: TaskOutput) -> Tuple[bool, Any]:
    """验证和解析 JSON 输出。"""
    try:
        # 尝试解析为 JSON
        data = json.loads(result)
        return (True, data)
    except json.JSONDecodeError as e:
        return (False, "无效的 JSON 格式")

task = Task(
    description="生成 JSON 报告",
    expected_output="有效的 JSON 对象",
    agent=analyst,
    guardrail=validate_json_output,
    guardrail_max_retries=3  # 限制重试尝试次数
)
```

## 从任务获取结构化一致的输出

<Note>
  还要注意，团队的最终任务的输出成为整个团队的最终输出。
</Note>

### 使用 `output_pydantic`

`output_pydantic` 属性允许您定义任务输出应遵循的 Pydantic 模型。这确保输出不仅是结构化的，而且还根据 Pydantic 模型进行验证。

以下演示如何使用 output\_pydantic 的示例：

```python Code theme={null}
import json

from crewai import Agent, Crew, Process, Task
from pydantic import BaseModel


class Blog(BaseModel):
    title: str
    content: str


blog_agent = Agent(
    role="博客内容生成代理",
    goal="生成博客标题和内容",
    backstory="""你是一位专家内容创作者，擅长制作引人入胜和信息丰富的博客文章。""",
    verbose=False,
    allow_delegation=False,
    llm="gpt-4o",
)

task1 = Task(
    description="""创建关于给定主题的博客标题和内容。确保内容在 200 字以内。""",
    expected_output="一个引人注目的博客标题和写得很好的内容。",
    agent=blog_agent,
    output_pydantic=Blog,
)

# 使用顺序流程实例化您的团队
crew = Crew(
    agents=[blog_agent],
    tasks=[task1],
    verbose=True,
    process=Process.sequential,
)

result = crew.kickoff()

# 选项 1：使用字典样式索引访问属性
print("访问属性 - 选项 1")
title = result["title"]
content = result["content"]
print("标题:", title)
print("内容:", content)

# 选项 2：直接从 Pydantic 模型访问属性
print("访问属性 - 选项 2")
title = result.pydantic.title
content = result.pydantic.content
print("标题:", title)
print("内容:", content)

# 选项 3：使用 to_dict() 方法访问属性
print("访问属性 - 选项 3")
output_dict = result.to_dict()
title = output_dict["title"]
content = output_dict["content"]
print("标题:", title)
print("内容:", content)

# 选项 4：打印整个博客对象
print("访问属性 - 选项 5")
print("博客:", result)

```

在此示例中：

* 定义了一个包含 title 和 content 字段的 Pydantic 模型 Blog。
* 任务 task1 使用 output\_pydantic 属性指定其输出应符合 Blog 模型。
* 执行团队后，您可以如所示以多种方式访问结构化输出。

#### 访问输出的说明

1. 字典样式索引：您可以使用 result\["field\_name"] 直接访问字段。这是可行的，因为 CrewOutput 类实现了 **getitem** 方法。
2. 直接从 Pydantic 模型：从 result.pydantic 对象直接访问属性。
3. 使用 to\_dict() 方法：将输出转换为字典并访问字段。
4. 打印整个对象：只需打印 result 对象即可查看结构化输出。

### 使用 `output_json`

`output_json` 属性允许您以 JSON 格式定义预期输出。这确保任务的输出是有效的 JSON 结构，可以在应用程序中轻松解析和使用。

以下演示如何使用 `output_json` 的示例：

```python Code theme={null}
import json

from crewai import Agent, Crew, Process, Task
from pydantic import BaseModel


# 为博客定义 Pydantic 模型
class Blog(BaseModel):
    title: str
    content: str


# 定义代理
blog_agent = Agent(
    role="博客内容生成代理",
    goal="生成博客标题和内容",
    backstory="""你是一位专家内容创作者，擅长制作引人入胜和信息丰富的博客文章。""",
    verbose=False,
    allow_delegation=False,
    llm="gpt-4o",
)

# 定义带有 output_json 设置为 Blog 模型的任务
task1 = Task(
    description="""创建关于给定主题的博客标题和内容。确保内容在 200 字以内。""",
    expected_output="带有 'title' 和 'content' 字段的 JSON 对象。",
    agent=blog_agent,
    output_json=Blog,
)

# 使用顺序流程实例化团队
crew = Crew(
    agents=[blog_agent],
    tasks=[task1],
    verbose=True,
    process=Process.sequential,
)

# 启动团队执行任务
result = crew.kickoff()

# 选项 1：使用字典样式索引访问属性
print("访问属性 - 选项 1")
title = result["title"]
content = result["content"]
print("标题:", title)
print("内容:", content)

# 选项 2：打印整个博客对象
print("访问属性 - 选项 2")
print("博客:", result)
```

在此示例中：

* 定义了一个包含 title 和 content 字段的 Pydantic 模型 Blog，用于指定 JSON 输出的结构。
* 任务 task1 使用 output\_json 属性指示它期望符合 Blog 模型的 JSON 输出。
* 执行团队后，您可以如所示以两种方式访问结构化的 JSON 输出。

#### 访问输出的说明

1. 使用字典样式索引访问属性：您可以使用 result\["field\_name"] 直接访问字段。这是可行的，因为 CrewOutput 类实现了 **getitem** 方法，允许您像对待字典一样对待输出。在此选项中，我们从结果中检索标题和内容。
2. 打印整个博客对象：通过打印 result，您得到 CrewOutput 对象的字符串表示。由于 **str** 方法被实现为返回 JSON 输出，这将显示整个输出作为表示 Blog 对象的格式化字符串。

***

通过使用 output\_pydantic 或 output\_json，您可以确保您的任务以一致和结构化的格式生成输出，从而更容易在应用程序内或跨多个任务处理和利用数据。

## 将工具与任务集成

利用 [CrewAI 工具包](https://github.com/joaomdmoura/crewai-tools) 和 [LangChain 工具](https://python.langchain.com/docs/integrations/tools) 来增强任务性能和代理交互。

## 创建带工具的任务

```python Code theme={null}
import os
os.environ["OPENAI_API_KEY"] = "您的密钥"
os.environ["SERPER_API_KEY"] = "您的密钥" # serper.dev API 密钥

from crewai import Agent, Task, Crew
from crewai_tools import SerperDevTool

research_agent = Agent(
  role='研究员',
  goal='查找并总结最新的 AI 新闻',
  backstory="""你是一家大型公司的研究员。
  你负责分析数据并为业务提供见解。""",
  verbose=True
)

# 在互联网上对文本内容执行指定查询的语义搜索
search_tool = SerperDevTool()

task = Task(
  description='查找并总结最新的 AI 新闻',
  expected_output='前 5 条最重要的 AI 新闻的要点列表摘要',
  agent=research_agent,
  tools=[search_tool]
)

crew = Crew(
    agents=[research_agent],
    tasks=[task],
    verbose=True
)

result = crew.kickoff()
print(result)
```

这演示了带有特定工具的任务如何覆盖代理的默认工具集，以便定制任务执行。

## 引用其他任务

在 CrewAI 中，一个任务的输出会自动传递到下一个任务，但您可以明确定义哪些任务的输出（包括多个任务）应作为另一个任务的上下文使用。

这在您有一个依赖于另一个任务输出的任务时很有用，而该任务不是紧随其后执行的。这是通过任务的 `context` 属性完成的：

```python Code theme={null}
# ...

research_ai_task = Task(
    description="研究 AI 的最新发展",
    expected_output="最近 AI 发展的列表",
    async_execution=True,
    agent=research_agent,
    tools=[search_tool]
)

research_ops_task = Task(
    description="研究 AI Ops 的最新发展",
    expected_output="最近 AI Ops 发展的列表",
    async_execution=True,
    agent=research_agent,
    tools=[search_tool]
)

write_blog_task = Task(
    description="写一篇关于 AI 的重要性及其最新新闻的完整博客文章",
    expected_output="一篇 4 段的完整博客文章",
    agent=writer_agent,
    context=[research_ai_task, research_ops_task]
)

#...
```

## 异步执行

您可以将任务定义为异步执行。这意味着团队不会等待它完成就继续下一个任务。这对于需要很长时间完成的任务或对执行下一个任务不重要的任务很有用。

然后，您可以使用 `context` 属性在未来的任务中定义它应等待异步任务的输出完成。

```python Code theme={null}
#...

list_ideas = Task(
    description="列出 5 个有趣的关于 AI 文章的探索想法。",
    expected_output="5 个文章想法的要点列表。",
    agent=researcher,
    async_execution=True # 将异步执行
)

list_important_history = Task(
    description="研究 AI 的历史并给我 5 个最重要的事件。",
    expected_output="5 个重要事件的要点列表。",
    agent=researcher,
    async_execution=True # 将异步执行
)

write_article = Task(
    description="写一篇关于 AI、其历史和有趣想法的文章。",
    expected_output="一篇 4 段关于 AI 的文章。",
    agent=writer,
    context=[list_ideas, list_important_history] # 将等待两个任务的输出完成
)

#...
```

## 回调机制

回调函数在任务完成后执行，允许基于任务结果触发操作或通知。

```python Code theme={null}
# ...

def callback_function(output: TaskOutput):
    # 在任务完成后执行某些操作
    # 示例：向经理发送电子邮件
    print(f"""
        任务完成！
        任务：{output.description}
        输出：{output.raw}
    """)

research_task = Task(
    description='查找并总结最新的 AI 新闻',
    expected_output='前 5 条最重要的 AI 新闻的要点列表摘要',
    agent=research_agent,
    tools=[search_tool],
    callback=callback_function
)

#...
```

## 访问特定任务输出

团队运行完成后，您可以使用任务对象的 `output` 属性访问特定任务的输出：

```python Code theme={null}
# ...
task1 = Task(
    description='查找并总结最新的 AI 新闻',
    expected_output='前 5 条最重要的 AI 新闻的要点列表摘要',
    agent=research_agent,
    tools=[search_tool]
)

#...

crew = Crew(
    agents=[research_agent],
    tasks=[task1, task2, task3],
    verbose=True
)

result = crew.kickoff()

# 返回带有任务描述和结果的 TaskOutput 对象
print(f"""
    任务完成！
    任务：{task1.output.description}
    输出：{task1.output.raw}
""")
```

## 工具覆盖机制

在任务中指定工具允许动态调整代理功能，强调 CrewAI 的灵活性。

## 错误处理和验证机制

在创建和执行任务时，存在某些验证机制以确保任务属性的健壮性和可靠性。这些包括但不限于：

* 确保每个任务只设置一种输出类型，以保持清晰的输出期望。
* 防止手动分配 `id` 属性，以维护唯一标识符系统的完整性。

这些验证有助于维护 crewAI 框架内任务执行的一致性和可靠性。

## 保存文件时创建目录

`create_directory` 参数控制 CrewAI 是否应在将任务输出保存到文件时自动创建目录。此功能对于组织输出和确保文件路径结构正确特别有用，尤其是在处理复杂的项目层次结构时。

### 默认行为

默认情况下，`create_directory=True`，这意味着 CrewAI 将自动创建输出文件路径中任何缺失的目录：

```python Code theme={null}
# 默认行为 - 自动创建目录
report_task = Task(
    description='生成综合市场分析报告',
    expected_output='带有图表和见解的详细市场分析',
    agent=analyst_agent,
    output_file='reports/2025/market_analysis.md',  # 如果 'reports/2025/' 不存在则创建它
    markdown=True
)
```

### 禁用目录创建

如果您想防止自动目录创建并确保目录已存在，请设置 `create_directory=False`：

```python Code theme={null}
# 严格模式 - 目录必须已存在
strict_output_task = Task(
    description='保存需要现有基础设施的关键数据',
    expected_output='保存到预配置位置的数据',
    agent=data_agent,
    output_file='secure/vault/critical_data.json',
    create_directory=False  # 如果 'secure/vault/' 不存在将引发 RuntimeError
)
```

### YAML 配置

您还可以在 YAML 任务定义中配置此行为：

```yaml tasks.yaml theme={null}
analysis_task:
  description: >
    生成季度财务分析
  expected_output: >
    带有季度见解的全面财务报告
  agent: financial_analyst
  output_file: reports/quarterly/q4_2024_analysis.pdf
  create_directory: true  # 自动创建 'reports/quarterly/' 目录

audit_task:
  description: >
    执行合规审计并保存到现有的审计目录
  expected_output: >
    合规审计报告
  agent: auditor
  output_file: audit/compliance_report.md
  create_directory: false  # 目录必须已存在
```

### 用例

**自动目录创建 (`create_directory=True`)：**

* 开发和原型环境
* 具有基于日期文件夹的动态报告生成
* 目录结构可能变化的自动化工作流程
* 具有用户特定文件夹的多租户应用程序

**手动目录管理 (`create_directory=False`)：**

* 具有严格文件系统控制的生产环境
* 目录必须预先配置的安全敏感应用程序
* 具有特定权限要求的系统
* 目录创建被审计的合规环境

### 错误处理

当 `create_directory=False` 且目录不存在时，CrewAI 将引发 `RuntimeError`：

```python Code theme={null}
try:
    result = crew.kickoff()
except RuntimeError as e:
    # 处理缺少目录错误
    print(f"目录创建失败: {e}")
    # 手动创建目录或使用备用位置
```

观看下面的视频，了解如何在 CrewAI 中使用结构化输出：

<iframe className="w-full aspect-video rounded-xl" src="https://www.youtube.com/embed/dNpKQk5uxHw" title="CrewAI 中的结构化输出" frameBorder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerPolicy="strict-origin-when-cross-origin" allowFullScreen />

## 结论

任务是 CrewAI 中代理行动的驱动力。
通过正确定义任务及其结果，您为 AI 代理有效工作奠定了基础，无论是独立还是作为协作单元。
为任务配备适当的工具、理解执行过程并遵循强大的验证实践对于最大化 CrewAI 的潜力至关重要，
确保代理为其任务做好有效准备，并按预期执行任务。