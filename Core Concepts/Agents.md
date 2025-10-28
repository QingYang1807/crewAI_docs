# 代理（Agents）

> 关于在 CrewAI 框架内创建和管理代理的详细指南。

## 代理概述

在 CrewAI 框架中，`Agent` 是一个可以自主执行以下操作的单元：

* 执行特定任务
* 根据其角色和目标做出决策
* 使用工具完成目标
* 与其他代理沟通和协作
* 维护交互记忆
* 在允许的情况下委托任务

<Tip>
  可以将代理视为具有特定技能、专业知识和责任的专门团队成员。例如，`Researcher`（研究员）代理可能擅长收集和分析信息，而`Writer`（作家）代理可能更擅长创建内容。
</Tip>

<Note type="info" title="企业增强功能：可视化代理构建器">
  CrewAI AMP 包含一个可视化代理构建器，无需编写代码即可简化代理的创建和配置。可视化设计您的代理并实时测试它们。

  <img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=c4f5428b111816273b3b53d9cef14fad" alt="可视化代理构建器截图" data-og-width="2654" width="2654" data-og-height="1710" height="1710" data-path="images/enterprise/crew-studio-interface.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=35ea9140f0b9e57da5f45adbc7e2f166 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=ae6f0c18ef3679b5466177710fbc4a94 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=6c3e2fe013ab4826da90c937a9855635 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=7f1474dd7f983532dc910363b96f783a 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=f1a6d7e744e6862af5e72dce4deb0fd1 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=74aeb1ccd8e2c8f84d4247b8d0259737 2500w" />

  可视化代理构建器支持：

  * 通过基于表单的界面直观配置代理
  * 实时测试和验证
  * 包含预配置代理类型的模板库
  * 轻松自定义代理属性和行为
</Note>

## 代理属性

| 属性                               | 参数                   | 类型                                  | 描述                                                                                              |
| :--------------------------------- | :--------------------- | :------------------------------------ | :------------------------------------------------------------------------------------------------ |
| **角色（Role）**                   | `role`                 | `str`                                 | 定义代理在团队中的功能和专业知识。                                                                |
| **目标（Goal）**                   | `goal`                 | `str`                                 | 指导代理决策的个体目标。                                                                          |
| **背景故事（Backstory）**          | `backstory`            | `str`                                 | 为代理提供上下文和个性，丰富交互体验。                                                            |
| **大语言模型（LLM）** *(可选)*     | `llm`                  | `Union[str, LLM, Any]`               | 为代理提供支持的语言模型。默认为 `OPENAI_MODEL_NAME` 中指定的模型或 "gpt-4"。                    |
| **工具（Tools）** *(可选)*         | `tools`                | `List[BaseTool]`                      | 代理可用到的能力或功能。默认为空列表。                                                            |
| **函数调用大语言模型** *(可选)*    | `function_calling_llm` | `Optional[Any]`                       | 用于工具调用的语言模型，如果指定则覆盖团队的大语言模型。                                          |
| **最大迭代次数** *(可选)*          | `max_iter`             | `int`                                 | 代理在提供最佳答案前的最大迭代次数。默认为 20。                                                   |
| **最大每分钟请求数** *(可选)*      | `max_rpm`              | `Optional[int]`                       | 每分钟最大请求数，以避免达到速率限制。                                                            |
| **最大执行时间** *(可选)*          | `max_execution_time`   | `Optional[int]`                       | 任务执行的最大时间（以秒为单位）。                                                                |
| **详细输出** *(可选)*              | `verbose`              | `bool`                                | 启用详细的执行日志以便调试。默认为 False。                                                        |
| **允许委托** *(可选)*              | `allow_delegation`     | `bool`                                | 允许代理将任务委托给其他代理。默认为 False。                                                      |
| **步骤回调** *(可选)*              | `step_callback`        | `Optional[Any]`                       | 每个代理步骤后调用的函数，覆盖团队回调。                                                          |
| **缓存** *(可选)*                  | `cache`                | `bool`                                | 启用工具使用的缓存。默认为 True。                                                                 |
| **系统模板** *(可选)*              | `system_template`      | `Optional[str]`                       | 代理的自定义系统提示模板。                                                                        |
| **提示模板** *(可选)*              | `prompt_template`      | `Optional[str]`                       | 代理的自定义提示模板。                                                                            |
| **响应模板** *(可选)*              | `response_template`    | `Optional[str]`                       | 代理的自定义响应模板。                                                                            |
| **允许代码执行** *(可选)*          | `allow_code_execution` | `Optional[bool]`                      | 启用代理的代码执行功能。默认为 False。                                                            |
| **最大重试限制** *(可选)*          | `max_retry_limit`      | `int`                                 | 发生错误时的最大重试次数。默认为 2。                                                              |
| **遵守上下文窗口** *(可选)*        | `respect_context_window`| `bool`                                | 通过总结保持消息在上下文窗口大小内。默认为 True。                                                 |
| **代码执行模式** *(可选)*          | `code_execution_mode`  | `Literal["safe", "unsafe"]`           | 代码执行模式：'safe'（使用 Docker）或 'unsafe'（直接执行）。默认为 'safe'。                      |
| **多模态** *(可选)*                | `multimodal`           | `bool`                                | 代理是否支持多模态功能。默认为 False。                                                            |
| **注入日期** *(可选)*              | `inject_date`          | `bool`                                | 是否自动将当前日期注入任务中。默认为 False。                                                      |
| **日期格式** *(可选)*              | `date_format`          | `str`                                 | 启用 inject\_date 时的日期格式字符串。默认为 "%Y-%m-%d"（ISO 格式）。                            |
| **推理** *(可选)*                  | `reasoning`            | `bool`                                | 代理是否应该在执行任务前进行反思并创建计划。默认为 False。                                         |
| **最大推理尝试次数** *(可选)*      | `max_reasoning_attempts`| `Optional[int]`                       | 执行任务前的最大推理尝试次数。如果为 None，则一直尝试直到准备就绪。                               |
| **嵌入器** *(可选)*                | `embedder`             | `Optional[Dict[str, Any]]`            | 代理使用的嵌入器配置。                                                                            |
| **知识源** *(可选)*                | `knowledge_sources`    | `Optional[List[BaseKnowledgeSource]]` | 代理可用的知识源。                                                                                |
| **使用系统提示** *(可选)*          | `use_system_prompt`    | `Optional[bool]`                      | 是否使用系统提示（用于支持 o1 模型）。默认为 True。                                               |

## 创建代理

在 CrewAI 中有两种创建代理的方式：使用 **YAML 配置（推荐）** 或 **直接在代码中定义**。

### YAML 配置（推荐）

使用 YAML 配置提供了一种更清晰、更易维护的方式来定义代理。我们强烈建议在 CrewAI 项目中使用这种方法。

按照 [安装](/en/installation) 部分中的说明创建 CrewAI 项目后，导航到 `src/latest_ai_development/config/agents.yaml` 文件，并修改模板以满足您的需求。

<Note>
  YAML 文件中的变量（如 `{topic}`）将在运行团队时从您的输入值替换：

  ```python Code theme={null}
  crew.kickoff(inputs={'topic': 'AI 代理'})
  ```
</Note>

以下是使用 YAML 配置代理的示例：

```yaml agents.yaml theme={null}
# src/latest_ai_development/config/agents.yaml
researcher:
  role: >
    {topic} 高级数据研究员
  goal: >
    揭示 {topic} 领域的最前沿发展
  backstory: >
    你是一位经验丰富的研究员，擅长发现 {topic} 领域的最新发展。
    以能够找到最相关的信息并以清晰简洁的方式呈现而闻名。

reporting_analyst:
  role: >
    {topic} 报告分析师
  goal: >
    基于 {topic} 数据分析和研究发现创建详细报告
  backstory: >
    你是一位一丝不苟的分析师，对细节有着敏锐的眼光。以能够将复杂数据转化为
    清晰简洁的报告而闻名，使他人能够轻松理解并基于你提供的信息采取行动。
```

要在代码中使用此 YAML 配置，创建一个继承自 `CrewBase` 的团队类：

```python Code theme={null}
# src/latest_ai_development/crew.py
from crewai import Agent, Crew, Process
from crewai.project import CrewBase, agent, crew
from crewai_tools import SerperDevTool

@CrewBase
class LatestAiDevelopmentCrew():
  """LatestAiDevelopment 团队"""

  agents_config = "config/agents.yaml"

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
```

<Note>
  您在 YAML 文件（`agents.yaml`）中使用的名称应与 Python 代码中的方法名称相匹配。
</Note>

### 直接代码定义

您可以通过实例化 `Agent` 类直接在代码中创建代理。以下是显示所有可用参数的综合示例：

```python Code theme={null}
from crewai import Agent
from crewai_tools import SerperDevTool

# 使用所有可用参数创建代理
agent = Agent(
    role="高级数据科学家",
    goal="分析和解释复杂数据集以提供可行的见解",
    backstory="拥有超过 10 年的数据科学和机器学习经验，"
              "你擅长在复杂数据集中找到模式。",
    llm="gpt-4",  # 默认值：OPENAI_MODEL_NAME 或 "gpt-4"
    function_calling_llm=None,  # 可选：用于工具调用的独立大语言模型
    verbose=False,  # 默认值：False
    allow_delegation=False,  # 默认值：False
    max_iter=20,  # 默认值：20 次迭代
    max_rpm=None,  # 可选：API 调用的速率限制
    max_execution_time=None,  # 可选：最大执行时间（以秒为单位）
    max_retry_limit=2,  # 默认值：错误时重试 2 次
    allow_code_execution=False,  # 默认值：False
    code_execution_mode="safe",  # 默认值："safe"（选项："safe"、"unsafe"）
    respect_context_window=True,  # 默认值：True
    use_system_prompt=True,  # 默认值：True
    multimodal=False,  # 默认值：False
    inject_date=False,  # 默认值：False
    date_format="%Y-%m-%d",  # 默认值：ISO 格式
    reasoning=False,  # 默认值：False
    max_reasoning_attempts=None,  # 默认值：None
    tools=[SerperDevTool()],  # 可选：工具列表
    knowledge_sources=None,  # 可选：知识源列表
    embedder=None,  # 可选：自定义嵌入器配置
    system_template=None,  # 可选：自定义系统提示模板
    prompt_template=None,  # 可选：自定义提示模板
    response_template=None,  # 可选：自定义响应模板
    step_callback=None,  # 可选：用于监控的回调函数
)
```

让我们分解一些常见用例的关键参数组合：

#### 基础研究代理

```python Code theme={null}
research_agent = Agent(
    role="研究分析师",
    goal="查找和总结关于特定主题的信息",
    backstory="你是一位注重细节的经验丰富的研究员",
    tools=[SerperDevTool()],
    verbose=True  # 启用日志记录以便调试
)
```

#### 代码开发代理

```python Code theme={null}
dev_agent = Agent(
    role="高级 Python 开发者",
    goal="编写和调试 Python 代码",
    backstory="拥有 10 年经验的专家 Python 开发者",
    allow_code_execution=True,
    code_execution_mode="safe",  # 使用 Docker 确保安全
    max_execution_time=300,  # 5 分钟超时
    max_retry_limit=3  # 复杂代码任务增加重试次数
)
```

#### 长时间运行的分析代理

```python Code theme={null}
analysis_agent = Agent(
    role="数据分析师",
    goal="对大型数据集进行深度分析",
    backstory="专长于大数据分析和模式识别",
    memory=True,
    respect_context_window=True,
    max_rpm=10,  # 限制 API 调用
    function_calling_llm="gpt-4o-mini"  # 使用更便宜的模型进行工具调用
)
```

#### 自定义模板代理

```python Code theme={null}
custom_agent = Agent(
    role="客户服务代表",
    goal="协助客户解决他们的问题",
    backstory="在客户支持方面经验丰富，注重满意度",
    system_template="""<|start_header_id|>system<|end_header_id|>
                        {{ .System }}<|eot_id|>""",
    prompt_template="""<|start_header_id|>user<|end_header_id|>
                        {{ .Prompt }}<|eot_id|>""",
    response_template="""<|start_header_id|>assistant<|end_header_id|>
                        {{ .Response }}<|eot_id|>""",
)
```

#### 具有日期感知和推理能力的代理

```python Code theme={null}
strategic_agent = Agent(
    role="市场分析师",
    goal="以精确的日期参考和战略规划跟踪市场动向",
    backstory="在时间敏感的金融分析和战略报告方面的专家",
    inject_date=True,  # 自动将当前日期注入任务
    date_format="%B %d, %Y",  # 格式化为 "2025年5月21日"
    reasoning=True,  # 启用战略规划
    max_reasoning_attempts=2,  # 限制规划迭代次数
    verbose=True
)
```

#### 推理代理

```python Code theme={null}
reasoning_agent = Agent(
    role="战略规划师",
    goal="分析复杂问题并创建详细的执行计划",
    backstory="专业的战略规划师，能够有条理地分解复杂的挑战",
    reasoning=True,  # 启用推理和规划
    max_reasoning_attempts=3,  # 限制推理尝试次数
    max_iter=30,  # 允许更多迭代进行复杂规划
    verbose=True
)
```

#### 多模态代理

```python Code theme={null}
multimodal_agent = Agent(
    role="视觉内容分析师",
    goal="分析和处理文本及视觉内容",
    backstory="专长于结合文本和图像理解的多模态分析",
    multimodal=True,  # 启用多模态功能
    verbose=True
)
```

### 参数详解

#### 关键参数

* `role`、`goal` 和 `backstory` 是必需的，塑造代理的行为
* `llm` 决定使用的语言模型（默认：OpenAI 的 GPT-4）

#### 内存和上下文

* `memory`：启用以维护对话历史
* `respect_context_window`：防止令牌限制问题
* `knowledge_sources`：添加特定领域的知识库

#### 执行控制

* `max_iter`：提供最佳答案前的最大尝试次数
* `max_execution_time`：以秒为单位的超时时间
* `max_rpm`：API 调用的速率限制
* `max_retry_limit`：错误时的重试次数

#### 代码执行

* `allow_code_execution`：必须设置为 True 才能运行代码
* `code_execution_mode`：
  * `"safe"`：使用 Docker（推荐用于生产环境）
  * `"unsafe"`：直接执行（仅在受信任的环境中使用）

<Note>
  这会运行默认的 Docker 镜像。如果你想配置 docker 镜像，请查看工具部分中的代码解释器工具。
  将代码解释器工具作为工具参数添加到代理中。
</Note>

#### 高级功能

* `multimodal`：启用多模态功能以处理文本和视觉内容
* `reasoning`：使代理能够在执行任务前进行反思和创建计划
* `inject_date`：自动将当前日期注入任务描述

#### 模板

* `system_template`：定义代理的核心行为
* `prompt_template`：结构化输入格式
* `response_template`：格式化代理响应

<Note>
  使用自定义模板时，确保同时定义 `system_template` 和 `prompt_template`。`response_template` 是可选的，但建议使用以保持一致的输出格式。
</Note>

<Note>
  使用自定义模板时，可以在模板中使用诸如 `{role}`、`{goal}` 和 `{backstory}` 之类的变量。这些将在执行期间自动填充。
</Note>

## 代理工具

代理可以配备各种工具以增强其能力。CrewAI 支持来自以下来源的工具：

* [CrewAI 工具包](https://github.com/joaomdmoura/crewai-tools)
* [LangChain 工具](https://python.langchain.com/docs/integrations/tools)

以下是如何向代理添加工具：

```python Code theme={null}
from crewai import Agent
from crewai_tools import SerperDevTool, WikipediaTools

# 创建工具
search_tool = SerperDevTool()
wiki_tool = WikipediaTools()

# 向代理添加工具
researcher = Agent(
    role="AI 技术研究员",
    goal="研究最新的 AI 发展",
    tools=[search_tool, wiki_tool],
    verbose=True
)
```

## 代理内存和上下文

代理可以维护其交互的记忆，并使用先前任务的上下文。这对于需要在多个任务之间保留信息的复杂工作流特别有用。

```python Code theme={null}
from crewai import Agent

analyst = Agent(
    role="数据分析师",
    goal="分析并记住复杂的数据模式",
    memory=True,  # 启用内存
    verbose=True
)
```

<Note>
  当启用 `memory` 时，代理将在多次交互中维护上下文，提高其处理复杂、多步骤任务的能力。
</Note>

## 上下文窗口管理

CrewAI 包含复杂的自动上下文窗口管理，用于处理对话超出语言模型令牌限制的情况。这个强大的功能由 `respect_context_window` 参数控制。

### 上下文窗口管理如何工作

当代理的对话历史对于 LLM 的上下文窗口来说过大时，CrewAI 会自动检测这种情况，并且可以：

1. **自动总结内容**（当 `respect_context_window=True` 时）
2. **停止执行并报错**（当 `respect_context_window=False` 时）

### 自动上下文处理（`respect_context_window=True`）

这是大多数用例的**默认和推荐设置**。启用时，CrewAI 将：

```python Code theme={null}
# 具有自动上下文管理的代理（默认）
smart_agent = Agent(
    role="研究分析师",
    goal="分析大型文档和数据集",
    backstory="擅长处理大量信息",
    respect_context_window=True,  # 🔑 默认：自动处理上下文限制
    verbose=True
)
```

**当超过上下文限制时会发生的情况：**

* ⚠️ **警告消息**：`"上下文长度超出。正在总结内容以适应模型上下文窗口。"`
* 🔄 **自动总结**：CrewAI 智能地总结对话历史
* ✅ **继续执行**：任务执行与总结的上下文无缝继续
* 📝 **保留信息**：关键信息被保留，同时减少令牌数量

### 严格上下文限制（`respect_context_window=False`）

当你需要精确控制并希望执行停止而不是丢失任何信息时：

```python Code theme={null}
# 具有严格上下文限制的代理
strict_agent = Agent(
    role="法律文档审查员",
    goal="提供精确的法律分析而不丢失信息",
    backstory="需要完整上下文进行准确分析的法律专家",
    respect_context_window=False,  # ❌ 在上下文限制时停止执行
    verbose=True
)
```

**当超过上下文限制时会发生的情况：**

* ❌ **错误消息**：`"上下文长度超出。考虑使用较小的文本或来自 crewai_tools 的 RAG 工具。"`
* 🛑 **执行停止**：任务执行立即停止
* 🔧 **需要手动干预**：你需要修改你的方法

### 选择正确的设置

#### 使用 `respect_context_window=True`（默认）当：

* **处理大型文档**，可能会超过上下文限制
* **长时间运行的对话**，其中一些总结是可以接受的
* **研究任务**，一般上下文比确切细节更重要
* **原型设计和开发**，你希望有稳健的执行

```python Code theme={null}
# 完美适用于文档处理
document_processor = Agent(
    role="文档分析师",
    goal="从大型研究论文中提取见解",
    backstory="擅长分析广泛文档",
    respect_context_window=True,  # 优雅地处理大型文档
    max_iter=50,  # 允许更多迭代进行复杂分析
    verbose=True
)
```

#### 使用 `respect_context_window=False` 当：

* **精确性至关重要**，信息丢失是不可接受的
* **法律或医疗任务**需要完整上下文
* **代码审查**，缺少细节可能会引入错误
* **金融分析**，准确性至关重要

```python Code theme={null}
# 完美适用于精确性任务
precision_agent = Agent(
    role="代码安全审计员",
    goal="识别代码中的安全漏洞",
    backstory="需要完整代码上下文的安全专家",
    respect_context_window=False,  # 宁可失败也不进行不完整的分析
    max_retry_limit=1,  # 在上下文问题上快速失败
    verbose=True
)
```

### 大型数据的替代方法

处理非常大的数据集时，考虑这些策略：

#### 1. 使用 RAG 工具

```python Code theme={null}
from crewai_tools import RagTool

# 创建 RAG 工具进行大型文档处理
rag_tool = RagTool()

rag_agent = Agent(
    role="研究助理",
    goal="高效查询大型知识库",
    backstory="擅长使用 RAG 工具进行信息检索",
    tools=[rag_tool],  # 使用 RAG 而不是大型上下文窗口
    respect_context_window=True,
    verbose=True
)
```

#### 2. 使用知识源

```python Code theme={null}
# 使用知识源而不是大型提示
knowledge_agent = Agent(
    role="知识专家",
    goal="使用策划的知识回答问题",
    backstory="擅长利用结构化知识源",
    knowledge_sources=[your_knowledge_sources],  # 预处理的知识
    respect_context_window=True,
    verbose=True
)
```

### 上下文窗口最佳实践

1. **监控上下文使用**：启用 `verbose=True` 以查看上下文管理的实际运行情况
2. **设计以提高效率**：构建任务以最小化上下文累积
3. **使用适当的模型**：选择适合你任务的具有足够上下文窗口的 LLM
4. **测试两种设置**：尝试 `True` 和 `False` 以查看哪种更适合你的用例
5. **与 RAG 结合**：对于非常大的数据集，使用 RAG 工具而不是完全依赖上下文窗口

### 上下文问题故障排除

**如果你遇到上下文限制错误：**

```python Code theme={null}
# 快速修复：启用自动处理
agent.respect_context_window = True

# 更好的解决方案：对大型数据使用 RAG 工具
from crewai_tools import RagTool
agent.tools = [RagTool()]

# 替代方案：将任务分解为更小的部分
# 或使用知识源而不是大型提示
```

**如果自动总结丢失了重要信息：**

```python Code theme={null}
# 禁用自动总结并改用 RAG
agent = Agent(
    role="详细分析师",
    goal="保持完整信息的准确性",
    backstory="需要完整上下文的专家",
    respect_context_window=False,  # 不进行总结
    tools=[RagTool()],  # 对大型数据使用 RAG
    verbose=True
)
```

<Note>
  上下文窗口管理功能在后台自动工作。你不需要调用任何特殊函数 - 只需将 `respect_context_window` 设置为你偏好的行为，CrewAI 就会处理其余部分！
</Note>

## 使用 `kickoff()` 直接与代理交互

代理可以使用 `kickoff()` 方法直接使用，而无需通过任务或团队工作流。当你不需要完整的团队编排功能时，这提供了一种与代理交互的更简单方式。

### `kickoff()` 如何工作

`kickoff()` 方法允许你直接向代理发送消息并获得响应，类似于与 LLM 交互的方式，但具有代理的所有能力（工具、推理等）。

```python Code theme={null}
from crewai import Agent
from crewai_tools import SerperDevTool

# 创建代理
researcher = Agent(
    role="AI 技术研究员",
    goal="研究最新的 AI 发展",
    tools=[SerperDevTool()],
    verbose=True
)

# 使用 kickoff() 直接与代理交互
result = researcher.kickoff("语言模型的最新发展是什么？")

# 访问原始响应
print(result.raw)
```

### 参数和返回值

| 参数            | 类型                               | 描述                                                         |
| :-------------- | :--------------------------------- | :----------------------------------------------------------- |
| `messages`      | `Union[str, List[Dict[str, str]]]` | 字符串查询或包含角色/内容的消息字典列表                      |
| `response_format`| `Optional[Type[Any]]`              | 用于结构化输出的可选 Pydantic 模型                           |

该方法返回一个具有以下属性的 `LiteAgentOutput` 对象：

* `raw`：包含原始输出文本的字符串
* `pydantic`：解析的 Pydantic 模型（如果提供了 `response_format`）
* `agent_role`：产生输出的代理的角色
* `usage_metrics`：执行的令牌使用指标

### 结构化输出

你可以通过提供 Pydantic 模型作为 `response_format` 来获取结构化输出：

```python Code theme={null}
from pydantic import BaseModel
from typing import List

class ResearchFindings(BaseModel):
    main_points: List[str]
    key_technologies: List[str]
    future_predictions: str

# 获取结构化输出
result = researcher.kickoff(
    "总结 2025 年 AI 的最新发展",
    response_format=ResearchFindings
)

# 访问结构化数据
print(result.pydantic.main_points)
print(result.pydantic.future_predictions)
```

### 多条消息

你也可以提供对话历史作为消息字典列表：

```python Code theme={null}
messages = [
    {"role": "user", "content": "我需要关于大型语言模型的信息"},
    {"role": "assistant", "content": "我很乐意帮助！你具体想了解什么？"},
    {"role": "user", "content": "2025 年的最新发展是什么？"}
]

result = researcher.kickoff(messages)
```

### 异步支持

通过 `kickoff_async()` 提供具有相同参数的异步版本：

```python Code theme={null}
import asyncio

async def main():
    result = await researcher.kickoff_async("AI 的最新发展是什么？")
    print(result.raw)

asyncio.run(main())
```

<Note>
  `kickoff()` 方法在内部使用 `LiteAgent`，它提供了更简单的执行流程，同时保留了代理的所有配置（角色、目标、背景故事、工具等）。
</Note>

## 重要考虑因素和最佳实践

### 安全和代码执行

* 使用 `allow_code_execution` 时，请谨慎处理用户输入并始终验证
* 在生产环境中使用 `code_execution_mode: "safe"`（Docker）
* 考虑设置适当的 `max_execution_time` 限制以防止无限循环

### 性能优化

* 使用 `respect_context_window: true` 防止令牌限制问题
* 设置适当的 `max_rpm` 以避免速率限制
* 启用 `cache: true` 以提高重复任务的性能
* 根据任务复杂性调整 `max_iter` 和 `max_retry_limit`

### 内存和上下文管理

* 利用 `knowledge_sources` 获取特定领域的信息
* 使用自定义嵌入模型时配置 `embedder`
* 使用自定义模板（`system_template`、`prompt_template`、`response_template`）对代理行为进行细粒度控制

### 高级功能

* 对于需要计划和反思复杂任务的代理，启用 `reasoning: true`
* 设置适当的 `max_reasoning_attempts` 以控制规划迭代次数（None 表示无限尝试）
* 对于时间敏感任务，使用 `inject_date: true` 为代理提供当前日期感知
* 使用标准的 Python 日期时间格式代码通过 `date_format` 自定义日期格式
* 对于需要处理文本和视觉内容的代理，启用 `multimodal: true`

### 代理协作

* 当代理需要协同工作时，启用 `allow_delegation: true`
* 使用 `step_callback` 监控和记录代理交互
* 考虑为不同的目的使用不同的 LLM：
  * 用于复杂推理的主要 `llm`
  * 用于高效工具使用的 `function_calling_llm`

### 日期感知和推理

* 对于时间敏感任务，使用 `inject_date: true` 为代理提供当前日期感知
* 使用标准的 Python 日期时间格式代码通过 `date_format` 自定义日期格式
* 有效的格式代码包括：%Y（年）、%m（月）、%d（日）、%B（完整月份名称）等
* 无效的日期格式将记录为警告，并且不会修改任务描述
* 对于受益于前期规划和反思的复杂任务，启用 `reasoning: true`

### 模型兼容性

* 对于不支持系统消息的旧模型，设置 `use_system_prompt: false`
* 确保你选择的 `llm` 支持你需要的功能（如函数调用）

## 常见问题故障排除

1. **速率限制**：如果你遇到 API 速率限制：
   * 实施适当的 `max_rpm`
   * 对重复操作使用缓存
   * 考虑批量请求

2. **上下文窗口错误**：如果你超过上下文限制：
   * 启用 `respect_context_window`
   * 使用更高效的提示
   * 定期清除代理内存

3. **代码执行问题**：如果代码执行失败：
   * 验证安全模式是否安装了 Docker
   * 检查执行权限
   * 审查代码沙箱设置

4. **内存问题**：如果代理响应看起来不一致：
   * 检查知识源配置
   * 审查对话历史管理

请记住，代理在根据其特定用例配置时最有效。花时间了解你的需求并相应调整这些参数。