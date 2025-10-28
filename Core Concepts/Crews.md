# Crews(团队)

> 理解和利用crewAI框架中的Crew（团队），包含全面的属性和功能。

## 概述

在crewAI中，一个Crew（团队）代表一个协作的Agent组，他们共同工作以完成一系列任务。每个Crew（团队）定义了任务执行、Agent协作和整体工作流程的策略。

## Crew（团队）属性

| 属性 | 参数 | 描述 | |
| :--- | :--- | :--- | :--- |
| **任务** | `tasks` | 分配给Crew（团队）的任务列表。 | |
| **Agent** | `agents` | 属于该Crew（团队）的Agent列表。 | |
| **流程** *(可选)* | `process` | Crew（团队）遵循的流程(如顺序sequential、层级hierarchical)。默认为`sequential`(顺序)。 | |
| **详细输出** *(可选)* | `verbose` | 执行期间日志的详细级别。默认为`False`。 | |
| **管理器LLM** *(可选)* | `manager_llm` | 在层级流程中，管理器Agent使用的语言模型。**使用层级流程时必需。** | |
| **函数调用LLM** *(可选)* | `function_calling_llm` | 如果传递此参数，Crew（团队）将使用此LLM为Crew（团队）中所有Agent的工具执行函数调用。每个Agent可以有自己的LLM，这会覆盖Crew（团队）的函数调用LLM。 | |
| **配置** *(可选)* | `config` | Crew（团队）的可选配置设置，格式为`Json`或`Dict[str, Any]`。 | |
| **最大RPM** *(可选)* | `max_rpm` | Crew（团队）在执行期间遵守的最大每分钟请求数。默认为`None`。 | |
| **记忆** *(可选)* | `memory` | 用于存储执行记忆(短期、长期、实体记忆)。 | |
| **缓存** *(可选)* | `cache` | 指定是否使用缓存来存储工具执行的结果。默认为`True`。 | |
| **嵌入器** *(可选)* | `embedder` | Crew（团队）要使用的嵌入器的配置。目前主要由记忆功能使用。默认为`{"provider": "openai"}`。 | |
| **步骤回调** *(可选)* | `step_callback` | 在每个Agent的每个步骤之后调用的函数。这可用于记录Agent的操作或执行其他操作；它不会覆盖Agent特定的`step_callback`。 | |
| **任务回调** *(可选)* | `task_callback` | 在每个任务完成后调用的函数。用于监控或任务执行后的额外操作。 | |
| **共享Crew（团队）** *(可选)* | `share_crew` | 是否希望将完整的Crew（团队）信息和执行与 crewAI 团队共享，以改进库，并允许我们训练模型。 | |
| **输出日志文件** *(可选)* | `output_log_file` | 设置为True以在当前目录中将日志保存为logs.txt，或提供文件路径。如果文件名以.json结尾，日志将为JSON格式，否则为.txt格式。默认为`None`。 | |
| **管理器Agent** *(可选)* | `manager_agent` | `manager`设置将用作管理器的自定义Agent。 | |
| **提示文件** *(可选)* | `prompt_file` | 用于Crew（团队）的提示JSON文件的路径。 | |
| **规划** *(可选)* | `planning` | 为Crew（团队）添加规划能力。在每次Crew（团队）迭代之前激活时，所有Crew（团队）数据将被发送到AgentPlanner，该规划器将规划任务，并且此计划将被添加到每个任务描述中。 | |
| **规划LLM** *(可选)* | `planning_llm` | 在规划过程中AgentPlanner使用的语言模型。 | |
| **知识源** *(可选)* | `knowledge_sources` | Crew（团队）级别的知识源，所有Agent都可以访问。 | |

<Tip>
  **Crew（团队）最大RPM**：`max_rpm`属性设置Crew（团队）每分钟可以执行的最大请求数，以避免速率限制，如果设置了该属性，它将覆盖各个Agent的`max_rpm`设置。
</Tip>

## 创建Crew（团队）

在CrewAI中有两种创建Crew（团队）的方法：使用**YAML配置(推荐)**或**直接在代码中定义**。

### YAML配置(推荐)

使用YAML配置提供了一种更清晰、更易维护的方式来定义Crew（团队），并且与CrewAI项目中Agent和任务的定义方式一致。

按照[安装](/en/installation)部分概述的步骤创建CrewAI项目后，您可以在一个继承自`CrewBase`的类中定义您的Crew（团队），并使用装饰器来定义Agent、任务和Crew（团队）本身。

#### 带装饰器的示例Crew（团队）类

```python code theme={null}
from crewai import Agent, Crew, Task, Process
from crewai.project import CrewBase, agent, task, crew, before_kickoff, after_kickoff
from crewai.agents.agent_builder.base_agent import BaseAgent
from typing import List

@CrewBase
class YourCrewName:
    """您的Crew（团队）描述"""

    agents: List[BaseAgent]
    tasks: List[Task]

    # YAML配置文件的路径
    # 要查看YAML中定义的Agent和任务示例，请查看以下内容：
    # - 任务: https://docs.crewai.com/concepts/tasks#yaml-configuration-recommended
    # - Agent: https://docs.crewai.com/concepts/agents#yaml-configuration-recommended
    agents_config = 'config/agents.yaml'
    tasks_config = 'config/tasks.yaml'

    @before_kickoff
    def prepare_inputs(self, inputs):
        # 在Crew（团队）启动前修改输入
        inputs['additional_data'] = "一些额外信息"
        return inputs

    @after_kickoff
    def process_output(self, output):
        # 在Crew（团队）完成后修改输出
        output.raw += "\n启动后处理。"
        return output

    @agent
    def agent_one(self) -> Agent:
        return Agent(
            config=self.agents_config['agent_one'], # type: ignore[index]
            verbose=True
        )

    @agent
    def agent_two(self) -> Agent:
        return Agent(
            config=self.agents_config['agent_two'], # type: ignore[index]
            verbose=True
        )

    @task
    def task_one(self) -> Task:
        return Task(
            config=self.tasks_config['task_one'] # type: ignore[index]
        )

    @task
    def task_two(self) -> Task:
        return Task(
            config=self.tasks_config['task_two'] # type: ignore[index]
        )

    @crew
    def crew(self) -> Crew:
        return Crew(
            agents=self.agents,  # 由@agent装饰器自动收集
            tasks=self.tasks,    # 由@task装饰器自动收集
            process=Process.sequential,
            verbose=True,
        )
```

如何运行上述代码：

```python code theme={null}
YourCrewName().crew().kickoff(inputs={"any": "input here"})
```

<Note>
  任务将按照它们定义的顺序执行。
</Note>

`CrewBase`类以及这些装饰器自动化了Agent和任务的收集，减少了手动管理的需要。

#### 来自`annotations.py`的装饰器概述

CrewAI在`annotations.py`文件中提供了几个装饰器，用于标记Crew（团队）类中的方法以进行特殊处理：

* `@CrewBase`：将类标记为Crew（团队）基类。
* `@agent`：表示返回`Agent`对象的方法。
* `@task`：表示返回`Task`对象的方法。
* `@crew`：表示返回`Crew`对象的方法。
* `@before_kickoff`：(可选)标记在Crew（团队）启动前执行的方法。
* `@after_kickoff`：(可选)标记在Crew（团队）完成后执行的方法。

这些装饰器有助于组织Crew（团队）的结构，并自动收集Agent和任务，而无需手动列出它们。

### 直接代码定义(替代方法)

或者，您也可以直接在代码中定义Crew（团队），而不使用YAML配置文件。

```python code theme={null}
from crewai import Agent, Crew, Task, Process
from crewai_tools import YourCustomTool

class YourCrewName:
    def agent_one(self) -> Agent:
        return Agent(
            role="数据分析师",
            goal="分析市场数据趋势",
            backstory="一位具有经济学背景的资深数据分析师",
            verbose=True,
            tools=[YourCustomTool()]
        )

    def agent_two(self) -> Agent:
        return Agent(
            role="市场研究员",
            goal="收集市场动态信息",
            backstory="一位勤奋的研究员，对细节有敏锐的洞察力",
            verbose=True
        )

    def task_one(self) -> Task:
        return Task(
            description="收集最近的市场数据并确定趋势。",
            expected_output="总结市场关键趋势的报告。",
            agent=self.agent_one()
        )

    def task_two(self) -> Task:
        return Task(
            description="研究影响市场动态的因素。",
            expected_output="影响市场因素的分析。",
            agent=self.agent_two()
        )

    def crew(self) -> Crew:
        return Crew(
            agents=[self.agent_one(), self.agent_two()],
            tasks=[self.task_one(), self.task_two()],
            process=Process.sequential,
            verbose=True
        )
```

如何运行上述代码：

```python code theme={null}
YourCrewName().crew().kickoff(inputs={})
```

在此示例中：

* Agent和任务直接在类中定义，不使用装饰器。
* 我们手动创建和管理Agent和任务列表。
* 这种方法提供了更多的控制，但对于较大的项目可能可维护性较差。

## Crew（团队）输出

在CrewAI框架中，Crew（团队）的输出被封装在`CrewOutput`类中。
这个类提供了一种结构化的方式来访问Crew（团队）执行的结果，包括各种格式，如原始字符串、JSON和Pydantic模型。
`CrewOutput`包括最终任务输出的结果、令牌使用情况和各个任务的输出。

### Crew（团队）输出属性

| 属性 | 参数 | 类型 | 描述 |
| :--- | :--- | :--- | :--- |
| **原始输出** | `raw` | `str` | Crew（团队）的原始输出。这是输出的默认格式。 |
| **Pydantic** | `pydantic` | `Optional[BaseModel]` | 表示Crew（团队）结构化输出的Pydantic模型对象。 |
| **JSON字典** | `json_dict` | `Optional[Dict[str, Any]]` | 表示Crew（团队）JSON输出的字典。 |
| **任务输出** | `tasks_output` | `List[TaskOutput]` | `TaskOutput`对象的列表，每个对象表示Crew（团队）中一个任务的输出。 |
| **令牌使用情况** | `token_usage` | `Dict[str, Any]` | 令牌使用情况的摘要，提供执行期间语言模型性能的洞察。 |

### Crew（团队）输出方法和属性

| 方法/属性 | 描述 |
| :--- | :--- |
| **json** | 如果输出格式是JSON，则返回Crew（团队）输出的JSON字符串表示形式。 |
| **to\_dict** | 将JSON和Pydantic输出转换为字典。 |
| **\_\_str\_\_** | 返回Crew（团队）输出的字符串表示形式，优先级为Pydantic、JSON，然后是原始输出。 |

### 访问Crew（团队）输出

Crew（团队）执行后，可以通过`Crew`对象的`output`属性访问其输出。`CrewOutput`类提供了多种方式来交互和呈现此输出。

#### 示例

```python Code theme={null}
# 示例Crew（团队）执行
crew = Crew(
    agents=[research_agent, writer_agent],
    tasks=[research_task, write_article_task],
    verbose=True
)

crew_output = crew.kickoff()

# 访问Crew（团队）输出
print(f"原始输出: {crew_output.raw}")
if crew_output.json_dict:
    print(f"JSON输出: {json.dumps(crew_output.json_dict, indent=2)}")
if crew_output.pydantic:
    print(f"Pydantic输出: {crew_output.pydantic}")
print(f"任务输出: {crew_output.tasks_output}")
print(f"令牌使用情况: {crew_output.token_usage}")
```

## 访问Crew（团队）日志

您可以通过将`output_log_file`设置为`True`(布尔值)或`file_name`(字符串)来查看Crew（团队）执行的实时日志。支持将事件记录为`file_name.txt`和`file_name.json`。
如果为`True`(布尔值)，将保存为`logs.txt`。

如果`output_log_file`设置为`False`(布尔值)或`None`，则不会填充日志。

```python Code theme={null}
# 保存Crew（团队）日志
crew = Crew(output_log_file = True)  # 日志将保存为logs.txt
crew = Crew(output_log_file = file_name)  # 日志将保存为file_name.txt
crew = Crew(output_log_file = file_name.txt)  # 日志将保存为file_name.txt
crew = Crew(output_log_file = file_name.json)  # 日志将保存为file_name.json
```

## 记忆利用

Crew（团队）可以利用记忆(短期、长期和实体记忆)来增强其执行和随时间的学习。此功能允许Crew（团队）存储和回忆执行记忆，有助于决策和任务执行策略。

## 缓存利用

可以使用缓存来存储工具执行的结果，通过减少重复执行相同任务的需求来提高效率。

## Crew（团队）使用指标

Crew（团队）执行后，您可以访问`usage_metrics`属性以查看Crew（团队）执行的所有任务的语言模型(LLM)使用指标。这提供了对运营效率和改进领域的洞察。

```python Code theme={null}
# 访问Crew（团队）的使用指标
crew = Crew(agents=[agent1, agent2], tasks=[task1, task2])
crew.kickoff()
print(crew.usage_metrics)
```

## Crew（团队）执行流程

* **顺序流程**：任务一个接一个地执行，允许线性工作流程。
* **层级流程**：管理器Agent协调Crew（团队），委派任务并在继续之前验证结果。**注意**：此过程需要`manager_llm`或`manager_agent`，并且对于验证流程至关重要。

### 启动Crew（团队）

一旦您的Crew（团队）组建完成，使用`kickoff()`方法启动工作流程。这将根据定义的流程启动执行过程。

```python Code theme={null}
# 启动Crew（团队）的任务执行
result = my_crew.kickoff()
print(result)
```

### 启动Crew（团队）的不同方式

一旦您的Crew（团队）组建完成，使用适当的启动方法启动工作流程。CrewAI提供了几种方法来更好地控制启动过程：`kickoff()`、`kickoff_for_each()`、`kickoff_async()`和`kickoff_for_each_async()`。

* `kickoff()`：根据定义的流程启动执行过程。
* `kickoff_for_each()`：为集合中每个提供的输入事件或项目顺序执行任务。
* `kickoff_async()`：异步启动工作流程。
* `kickoff_for_each_async()`：为每个提供的输入事件或项目并发执行任务，利用异步处理。

```python Code theme={null}
# 启动Crew（团队）的任务执行
result = my_crew.kickoff()
print(result)

# 使用kickoff_for_each的示例
inputs_array = [{'topic': '医疗保健中的AI'}, {'topic': '金融中的AI'}]
results = my_crew.kickoff_for_each(inputs=inputs_array)
for result in results:
    print(result)

# 使用kickoff_async的示例
inputs = {'topic': '医疗保健中的AI'}
async_result = await my_crew.kickoff_async(inputs=inputs)
print(async_result)

# 使用kickoff_for_each_async的示例
inputs_array = [{'topic': '医疗保健中的AI'}, {'topic': '金融中的AI'}]
async_results = await my_crew.kickoff_for_each_async(inputs=inputs_array)
for async_result in async_results:
    print(async_result)
```

这些方法在管理和执行Crew（团队）中的任务方面提供了灵活性，允许根据您的需求定制同步和异步工作流程。

### 从特定任务重放

您现在可以使用我们的CLI命令`replay`从特定任务重放。

CrewAI中的重放功能允许您使用命令行界面(CLI)从特定任务重放。通过运行命令`crewai replay -t <task_id>`，您可以为重放过程指定`task_id`。

启动现在将保存最新的启动返回的任务输出在本地，以便您能够从中重放。

### 使用CLI从特定任务重放

要使用重放功能，请按照以下步骤操作：

1. 打开您的终端或命令提示符。
2. 导航到CrewAI项目所在的目录。
3. 运行以下命令：

要查看最新的启动任务ID，请使用：

```shell  theme={null}
crewai log-tasks-outputs
```

然后，要从特定任务重放，请使用：

```shell  theme={null}
crewai replay -t <task_id>
```

这些命令允许您从最新的启动任务重放，仍然保留以前执行任务的上下文。