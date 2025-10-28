# 层次化流程

> 一份全面指南，帮助您理解和应用 CrewAI 项目中的层次化流程，已更新以反映最新的编码实践和功能。

## 简介

CrewAI 中的层次化流程引入了一种结构化的任务管理方法，模拟传统组织层次结构以实现高效的任务委派和执行。
这种系统化工作流程通过确保任务以最佳效率和准确性处理，从而提升项目成果。

<Tip>
  层次化流程设计用于利用像 GPT-4 这样的高级模型，在处理复杂任务时优化令牌使用，同时提高效率。
</Tip>

## 层次化流程概述

默认情况下，CrewAI 中的任务通过顺序流程管理。然而，采用层次化方法可以在任务管理中建立清晰的层次结构，
其中"管理器"代理协调工作流程、委派任务并验证结果，以实现简化高效的执行。这个管理器代理现在可以由
CrewAI 自动创建或由用户显式设置。

### 主要特点

* **任务委派**：管理器代理根据成员的角色和能力在他们之间分配任务。
* **结果验证**：管理器评估结果以确保它们符合所需标准。
* **高效工作流**：模拟公司结构，提供有组织的任务管理方法。
* **系统提示处理**：可选择指定系统是否应使用预定义提示。
* **停用词控制**：可选择指定是否应使用停用词，支持包括 o1 模型在内的各种模型。
* **上下文窗口尊重**：通过启用尊重上下文窗口来优先考虑重要上下文，这现在是默认行为。
* **委派控制**：委派现在默认禁用以给用户显式控制权。
* **每分钟最大请求数**：可配置选项以设置每分钟的最大请求数。
* **最大迭代次数**：限制获取最终答案的最大迭代次数。

## 实现层次化流程

要使用层次化流程，必须明确将 process 属性设置为 `Process.hierarchical`，因为默认行为是 `Process.sequential`。
定义一个具有指定管理器的团队，并建立清晰的指挥链。

<Tip>
  在代理级别分配工具，以便在管理器指导下促进指定代理的任务委派和执行。
  也可以在任务级别指定工具，以精确控制任务执行期间的工具可用性。
</Tip>

<Tip>
  配置 `manager_llm` 参数对层次化流程至关重要。
  系统需要设置管理器 LLM 才能正常运行，确保定制的决策制定。
</Tip>

```python
from crewai import Crew, Process, Agent

# 代理使用背景故事、缓存和详细模式等属性定义
researcher = Agent(
    role='Researcher',
    goal='Conduct in-depth analysis',
    backstory='Experienced data analyst with a knack for uncovering hidden trends.',
)
writer = Agent(
    role='Writer',
    goal='Create engaging content',
    backstory='Creative writer passionate about storytelling in technical domains.',
)

# 使用层次化流程和额外配置建立团队
project_crew = Crew(
    tasks=[...],  # 要在管理器监督下委派和执行的任务
    agents=[researcher, writer],
    manager_llm="gpt-4o",  # 指定管理器应使用的 LLM
    process=Process.hierarchical,
    planning=True, 
)
```

### 使用自定义管理器代理

或者，您可以创建一个具有特定属性的自定义管理器代理，这些属性专为您的项目管理需求量身定制。这使您可以更多地控制管理器的行为和能力。

```python
# 定义自定义管理器代理
manager = Agent(
    role="Project Manager",
    goal="Efficiently manage the crew and ensure high-quality task completion",
    backstory="You're an experienced project manager, skilled in overseeing complex projects and guiding teams to success.",
    allow_delegation=True,
)

# 在您的团队中使用自定义管理器
project_crew = Crew(
    tasks=[...],
    agents=[researcher, writer],
    manager_agent=manager,  # 使用您的自定义管理器代理
    process=Process.hierarchical,
    planning=True,
)
```

<Tip>
  有关创建和自定义管理器代理的更多详细信息，请查看[自定义管理器代理文档](https://docs.crewai.com/how-to/custom-manager-agent#custom-manager-agent)。
</Tip>

### 工作流程实战

1. **任务分配**：管理器策略性地分配任务，考虑每个代理的能力和可用工具。
2. **执行和审查**：代理完成他们的任务，可选择异步执行和回调函数以简化工作流程。
3. **顺序任务进展**：尽管是层次化流程，但任务遵循逻辑顺序以实现平稳进展，由管理器的监督促进。

## 结论

在 CrewAI 中采用层次化流程，通过正确的配置和对系统能力的理解，有助于实现有组织和高效的项目管理方法。
利用高级功能和自定义来调整工作流程以满足您的特定需求，确保最佳的任务执行和项目成功。