# 自定义提示词

> 深入探索 CrewAI 的底层提示词定制功能，为不同模型和语言实现超自定义和复杂的用例。

## 为何要自定义提示词？

尽管 CrewAI 的默认提示词在许多场景下都表现良好，但底层定制化可以显著提升代理行为的灵活性和强大性。以下是你可能需要利用这种深度控制的原因：

1.  **针对特定 LLM 进行优化** – 不同的模型（如 GPT-4、Claude 或 Llama）在与其独特架构相匹配的提示词格式下表现更佳。
2.  **更改语言** – 构建完全使用非英语语言的代理，精确处理语言中的细微差别。
3.  **为复杂领域专业化** – 为医疗保健、金融或法律等高度专业化的行业调整提示词。
4.  **调整语气和风格** – 使代理更正式、随意、更具创造性或更偏向分析。
5.  **支持超自定义用例** – 利用高级的提示词结构和格式来满足复杂、特定于项目的要求。

本指南将探讨如何从更底层访问 CrewAI 的提示词，让你能够精细地控制代理的思考和交互方式。

## 理解 CrewAI 的提示词系统

在底层，CrewAI 采用了一个模块化的提示词系统，你可以对其进行广泛的自定义：

*   **代理模板** – 控制每个代理处理其分配角色的方式。
*   **提示词片段** – 控制任务执行、工具使用和输出结构等专门行为。
*   **错误处理** – 指导代理如何响应失败、异常或超时。
*   **工具特定提示词** – 定义如何调用或使用工具的详细指令。

查看 [CrewAI 代码库中的原始提示词模板](https://github.com/crewAIInc/crewAI/blob/main/src/crewai/translations/en.json)，了解这些元素是如何组织的。从那里，你可以根据需要覆盖或调整它们，以解锁高级行为。

## 理解默认系统指令

<Warning>
  **生产环境透明度问题**：CrewAI 会自动向你的提示词中注入你可能并未察觉的默认指令。本节解释了底层发生了什么，以及如何获得完全的控制权。
</Warning>

当你使用 `role`、`goal` 和 `backstory` 定义一个代理时，CrewAI 会自动添加控制格式和行为的额外系统指令。对于需要完全提示词透明的生产系统，理解这些默认注入至关重要。

### CrewAI 自动注入的内容

根据你的代理配置，CrewAI 会添加不同的默认指令：

#### 对于不使用工具的代理

```text  theme={null}
“我必须使用这些格式，这关系到我的工作！”
```

#### 对于使用工具的代理

```text  theme={null}
“重要：在你的回复中使用以下格式：

思考：你应该始终思考要做什么
动作：要采取的动作，仅使用 [工具名称] 中的一个
动作输入：动作的输入，只是一个简单的 JSON 对象...
```

#### 对于结构化输出（JSON/Pydantic）

````text  theme={null}
“确保你的最终答案仅包含以下格式的内容：{output_format}
确保最终输出不包括任何像 ```json 或 ```python 这样的代码块标记。”
````

### 查看完整的系统提示词

要查看发送到你的 LLM 的确切提示词，你可以检查生成的提示词：

```python  theme={null}
from crewai import Agent, Crew, Task
from crewai.utilities.prompts import Prompts

# 创建你的代理
agent = Agent(
    role="Data Analyst",
    goal="分析数据并提供洞见",
    backstory="你是一位拥有10年经验的专家级数据分析师。",
    verbose=True
)

# 创建一个示例任务
task = Task(
    description="分析销售数据并识别趋势",
    expected_output="包含关键洞见和趋势的详细分析",
    agent=agent
)

# 创建提示词生成器
prompt_generator = Prompts(
    agent=agent,
    has_tools=len(agent.tools) > 0,
    use_system_prompt=agent.use_system_prompt
)

# 生成并检查实际的提示词
generated_prompt = prompt_generator.task_execution()

# 打印将发送到 LLM 的完整系统提示词
if "system" in generated_prompt:
    print("=== 系统提示词 ===")
    print(generated_prompt["system"])
    print("\n=== 用户提示词 ===")
    print(generated_prompt["user"])
else:
    print("=== 完整提示词 ===")
    print(generated_prompt["prompt"])

# 你也可以看到任务描述是如何被格式化的
print("\n=== 任务上下文 ===")
print(f"任务描述: {task.description}")
print(f"期望输出: {task.expected_output}")
```

### 覆盖默认指令

你有几个选项来获得对提示词的完全控制：

#### 选项 1：自定义模板（推荐）

```python  theme={null}
from crewai import Agent

# 定义你自己的不含默认指令的系统模板
custom_system_template = """你是 {role}。{backstory}
你的目标是：{goal}

请自然地、对话式地回应。专注于提供有帮助的、准确的信息。"""

custom_prompt_template = """任务：{input}

请认真完成此任务。"""

agent = Agent(
    role="研究助理",
    goal="帮助用户查找准确信息",
    backstory="你是一位乐于助人的研究助理。",
    system_template=custom_system_template,
    prompt_template=custom_prompt_template,
    use_system_prompt=True  # 使用独立的系统/用户消息
)
```

#### 选项 2：自定义提示词文件

创建一个 `custom_prompts.json` 文件来覆盖特定的提示词片段：

```json  theme={null}
{
  "slices": {
    "no_tools": "\n请以自然、对话的方式提供你的最佳答案。",
    "tools": "\n你可以访问这些工具：{tools}\n\n在有帮助时使用它们，但请自然地回应。",
    "formatted_task_instructions": "将你的回复格式化为：{output_format}"
  }
}
```

然后在你的团队中使用它：

```python  theme={null}
crew = Crew(
    agents=[agent],
    tasks=[task],
    prompt_file="custom_prompts.json",
    verbose=True
)
```

#### 选项 3：为 o1 模型禁用系统提示词

```python  theme={null}
agent = Agent(
    role="分析师",
    goal="分析数据",
    backstory="专家分析师",
    use_system_prompt=False  # 禁用系统提示词分离
)
```

### 使用可观测性工具进行调试

为了生产环境的透明度，可以集成可观测性平台来监控所有提示词和 LLM 交互。这让你能够精确看到发送到你的 LLM 的提示词（包括默认指令）。

请参阅我们的 [可观测性文档](/en/observability/overview)，了解与 Langfuse、MLflow、Weights & Biases 以及自定义日志解决方案等各种平台的详细集成指南。

### 生产环境的最佳实践

1.  **在部署到生产环境前务必检查生成的提示词**。
2.  **当你需要对提示词内容进行完全控制时，请使用自定义模板**。
3.  **集成可观测性工具**以进行持续的提示词监控（参见 [可观测性文档](/en/observability/overview)）。
4.  **使用不同的 LLM 进行测试**，因为默认指令在不同模型上的效果可能不同。
5.  **记录你的提示词自定义**，以便团队保持透明。

<Tip>
  默认指令的存在是为了确保代理行为的一致性，但它们可能会干扰特定于领域的需求。使用上述自定义选项，以便在生产系统中对你的代理行为保持完全控制。
</Tip>

## 管理提示词文件的最佳实践

在进行底层提示词定制时，请遵循以下准则，以保持条理清晰和易于维护：

1.  **文件分离** – 将自定义的提示词存储在主代码库之外的专用 JSON 文件中。
2.  **版本控制** – 在你的代码库中跟踪更改，确保清晰记录随时间推移的提示词调整。
3.  **按模型或语言组织** – 使用如 `prompts_llama.json` 或 `prompts_es.json` 等命名方案，以快速识别专门的配置。
4.  **记录更改** – 提供注释或维护一个 README 文件，详细说明你自定义的目的和范围。
5.  **最小化改动** – 只覆盖你真正需要调整的特定片段，保持其他所有功能的默认设置不变。

## 自定义提示词的最简单方法

一个直接的方法是为你想要覆盖的提示词创建一个 JSON 文件，然后让你的 Crew 指向该文件：

1.  创建一个包含你更新的提示词片段的 JSON 文件。
2.  通过 Crew 中的 `prompt_file` 参数引用该文件。

CrewAI 然后会将你的自定义与默认设置合并，因此你不必重新定义每个提示词。方法如下：

### 示例：基本的提示词自定义

创建一个包含你要修改的提示词的 `custom_prompts.json` 文件。确保列出它应包含的所有顶级提示词，而不仅仅是你的更改：

```json  theme={null}
{
  "slices": {
    "format": "回复时，请遵循以下结构：\n\n思考：你的分步思考过程\n动作：你正在使用的任何工具\n结果：你的最终答案或结论"
  }
}
```

然后像这样集成它：

```python  theme={null}
from crewai import Agent, Crew, Task, Process

# 像往常一样创建代理和任务
researcher = Agent(
    role="研究专家",
    goal="寻找关于量子计算的信息",
    backstory="你是一位量子物理学专家",
    verbose=True
)

research_task = Task(
    description="研究量子计算的应用",
    expected_output="一份实际应用的摘要",
    agent=researcher
)

# 使用你的自定义提示词文件创建一个团队
crew = Crew(
    agents=[researcher],
    tasks=[research_task],
    prompt_file="path/to/custom_prompts.json",
    verbose=True
)

# 运行团队
result = crew.kickoff()
```

仅通过这几处编辑，你就能获得对代理如何通信和解决任务的底层控制权。

## 针对特定模型进行优化

不同的模型在不同结构的提示词下表现更佳。进行更深层次的调整可以显著提升性能，使你的提示词与模型的细微差别保持一致。

### 示例：Llama 3.3 提示词模板

例如，在处理 Meta 的 Llama 3.3 时，更深层次的定制可能反映了在 [https://www.llama.com/docs/model-cards-and-prompt-formats/llama3\_1/#prompt-template](https://www.llama.com/docs/model-cards-and-prompt-formats/llama3_1/#prompt-template) 中描述的推荐结构。

以下是一个示例，说明如何在代码中微调代理以利用 Llama 3.3：

```python  theme={null}
from crewai import Agent, Crew, Task, Process
from crewai_tools import DirectoryReadTool, FileReadTool

# 为系统、用户（提示词）和助手（响应）消息定义模板
system_template = """<|begin_of_text|><|start_header_id|>system<|end_header_id|>{{ .System }}<|eot_id|>"""
prompt_template = """<|start_header_id|>user<|end_header_id|>{{ .Prompt }}<|eot_id|>"""
response_template = """<|start_header_id|>assistant<|end_header_id|>{{ .Response }}<|eot_id|>"""

# 使用 Llama 特定布局创建代理
principal_engineer = Agent(
    role="首席工程师",
    goal="监督 AI 架构并做出高级决策",
    backstory="你是负责关键 AI 系统的首席工程师",
    verbose=True,
    llm="groq/llama-3.3-70b-versatile",  # 使用 Llama 3 模型
    system_template=system_template,
    prompt_template=prompt_template,
    response_template=response_template,
    tools=[DirectoryReadTool(), FileReadTool()]
)

# 定义一个示例任务
engineering_task = Task(
    description="审查 AI 实现文件以寻找潜在的改进点",
    expected_output="一份关键发现和建议的摘要",
    agent=principal_engineer
)

# 为该任务创建一个 Crew
llama_crew = Crew(
    agents=[principal_engineer],
    tasks=[engineering_task],
    process=Process.sequential,
    verbose=True
)

# 执行 crew
result = llama_crew.kickoff()
print(result.raw)
```

通过这种更深层次的配置，你可以对基于 Llama 的工作流进行全面、底层的控制，而无需单独的 JSON 文件。

## 结论

CrewAI 中的底层提示词定制为超自定义、复杂的用例打开了大门。通过建立组织良好的提示词文件（或直接的内联模板），你可以适应各种模型、语言和专业领域。这种灵活性确保你可以精确地塑造所需的 AI 行为，同时，当你不覆盖它们时，CrewAI 仍然提供可靠的默认设置。

<Check>
  你现在掌握了在 CrewAI 中进行高级提示词定制的基础。无论是为模型特定的结构还是领域特定的约束进行调整，这种底层方法都能让你以高度专业化的方式塑造代理的交互。
</Check>