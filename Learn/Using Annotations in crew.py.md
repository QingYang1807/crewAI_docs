# 在 crew.py 中使用注解

> 了解如何使用注解在 CrewAI 中正确构建代理、任务和组件

本指南解释了如何在 `crew.py` 文件中使用注解来正确引用**代理**、**任务**和其他组件。

## 介绍

CrewAI 框架中的注解用于装饰类和方法，为团队中的各种组件提供元数据和功能。这些注解有助于组织和构建代码，使其更具可读性和可维护性。

## 可用注解

CrewAI 框架提供以下注解：

* `@CrewBase`：用于装饰主团队类。
* `@agent`：装饰定义并返回 Agent 对象的方法。
* `@task`：装饰定义并返回 Task 对象的方法。
* `@crew`：装饰创建并返回 Crew 对象的方法。
* `@llm`：装饰初始化并返回语言模型对象的方法。
* `@tool`：装饰初始化并返回工具对象的方法。
* `@callback`：用于定义回调方法。
* `@output_json`：用于输出 JSON 数据的方法。
* `@output_pydantic`：用于输出 Pydantic 模型的方法。
* `@cache_handler`：用于定义缓存处理方法。

## 使用示例

让我们通过示例来了解如何使用这些注解：

### 1. 团队基类

```python  theme={null}
@CrewBase
class LinkedinProfileCrew():
    """LinkedinProfile crew"""
    agents_config = 'config/agents.yaml'
    tasks_config = 'config/tasks.yaml'
```

`@CrewBase` 注解用于装饰主团队类。该类通常包含创建代理、任务和团队本身的配置和方法。

<Tip>
  `@CrewBase` 不仅仅是注册类：

  * **配置引导**：在类文件旁边查找 `agents_config` 和 `tasks_config`（默认为 `config/agents.yaml` 和 `config/tasks.yaml`），在实例化时加载它们，如果文件缺失则安全地回退到空字典。
  * **装饰器编排**：保留对每个用 `@agent`、`@task`、`@before_kickoff` 或 `@after_kickoff` 标记的方法的引用，使它们每个团队实例化一次，并按声明顺序执行。
  * **钩子连接**：自动将保留的启动钩子附加到 `@crew` 方法返回的 `Crew` 对象，使它们在 `.kickoff()` 之前和之后运行。
  * **MCP 集成**：当类定义 `mcp_server_params` 时，`get_mcp_tools()` 会延迟启动 MCP 服务器适配器，填充声明的工具，内部启动后钩子会停止适配器。有关适配器配置详细信息，请参阅 [MCP 概述](/en/mcp/overview)。
</Tip>

### 2. 工具定义

```python  theme={null}
@tool
def myLinkedInProfileTool(self):
    return LinkedInProfileTool()
```

`@tool` 注解用于装饰返回工具对象的方法。这些工具可以被代理用来执行特定任务。

### 3. LLM 定义

```python  theme={null}
@llm
def groq_llm(self):
    api_key = os.getenv('api_key')
    return ChatGroq(api_key=api_key, temperature=0, model_name="mixtral-8x7b-32768")
```

`@llm` 注解用于装饰初始化并返回语言模型对象的方法。这些 LLM 被代理用于自然语言处理任务。

### 4. 代理定义

```python  theme={null}
@agent
def researcher(self) -> Agent:
    return Agent(
        config=self.agents_config['researcher']
    )
```

`@agent` 注解用于装饰定义并返回 Agent 对象的方法。

### 5. 任务定义

```python  theme={null}
@task
def research_task(self) -> Task:
    return Task(
        config=self.tasks_config['research_linkedin_task'],
        agent=self.researcher()
    )
```

`@task` 注解用于装饰定义并返回 Task 对象的方法。这些方法指定任务配置和负责该任务的代理。

### 6. 团队创建

```python  theme={null}
@crew
def crew(self) -> Crew:
    """Creates the LinkedinProfile crew"""
    return Crew(
        agents=self.agents,
        tasks=self.tasks,
        process=Process.sequential,
        verbose=True
    )
```

`@crew` 注解用于装饰创建并返回 `Crew` 对象的方法。此方法将所有组件（代理和任务）组装成一个功能完整的团队。

## YAML 配置

代理配置通常存储在 YAML 文件中。以下是研究代理的 `agents.yaml` 文件示例：

```yaml  theme={null}
researcher:
    role: >
        LinkedIn 资深数据研究员
    goal: >
        根据提供的姓名 {name} 和域名 {domain} 发现详细的 LinkedIn 个人资料
        根据域名 {domain} 生成 Dall-E 图像
    backstory: >
        您是一位经验丰富的研究员，擅长发现最相关的 LinkedIn 个人资料。
        以高效浏览 LinkedIn 而闻名，您擅长清晰简洁地收集和呈现专业信息。
    allow_delegation: False
    verbose: True
    llm: groq_llm
    tools:
        - myLinkedInProfileTool
        - mySerperDevTool
        - myDallETool
```

这个 YAML 配置对应于 `LinkedinProfileCrew` 类中定义的研究代理。配置指定了代理的角色、目标、背景故事以及其他属性，如它使用的 LLM 和工具。

请注意，YAML 文件中的 `llm` 和 `tools` 如何与 Python 类中用 `@llm` 和 `@tool` 装饰的方法相对应。

## 最佳实践

* **一致的命名**：为方法使用清晰一致的命名约定。例如，代理方法可以以其角色命名（如 researcher、reporting\_analyst）。
* **环境变量**：使用环境变量存储 API 密钥等敏感信息。
* **灵活性**：通过允许轻松添加或删除代理和任务来设计灵活的团队。
* **YAML-代码对应**：确保 YAML 文件中的名称和结构与 Python 代码中装饰的方法正确对应。

通过遵循这些指导原则并正确使用注解，您可以使用 CrewAI 框架创建结构良好且易于维护的团队。