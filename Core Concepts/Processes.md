# 流程

> 关于CrewAI中通过流程进行工作流管理的详细指南，包含更新的实现细节。

## 概述

<Tip>
  流程协调代理执行任务的过程，类似于人类团队中的项目管理。
  这些流程确保任务按照预定义策略高效分配和执行。
</Tip>

## 流程实现

* **顺序流程（Sequential）**：按顺序执行任务，确保任务按有序进展完成。
* **层级流程（Hierarchical）**：将任务组织成管理层次结构，任务基于结构化的指挥链进行分配和执行。必须在团队中指定管理器语言模型（`manager_llm`）或自定义管理器代理（`manager_agent`）才能启用层级流程，从而促进管理器对任务的创建和管理。
* **共识流程（Consensual Process）（计划中）**：旨在实现代理之间关于任务执行的协作决策，这种流程类型为CrewAI内的任务管理引入了民主方法。该流程计划在未来开发，目前尚未在代码库中实现。

## 流程在团队合作中的作用

流程使各个代理能够作为一个有凝聚力的单元运作，简化他们的努力，以高效和协调的方式实现共同目标。

## 为团队分配流程

要为团队分配流程，请在创建团队时指定流程类型以设置执行策略。对于层级流程，确保为管理器代理定义`manager_llm`或`manager_agent`。

```python  theme={null}
from crewai import Crew, Process

# 示例：创建具有顺序流程的团队
crew = Crew(
    agents=my_agents,
    tasks=my_tasks,
    process=Process.sequential
)

# 示例：创建具有层级流程的团队
# 确保提供manager_llm或manager_agent
crew = Crew(
    agents=my_agents,
    tasks=my_tasks,
    process=Process.hierarchical,
    manager_llm="gpt-4o"
    # 或者
    # manager_agent=my_manager_agent
)
```

**注意：** 确保在创建`Crew`对象之前已定义`my_agents`和`my_tasks`，而对于层级流程，还需要提供`manager_llm`或`manager_agent`。

## 顺序流程

这种方法反映了动态团队工作流程，以深思熟虑和系统化的方式推进任务。任务执行遵循任务列表中预定义的顺序，一个任务的输出作为下一个任务的上下文。

要自定义任务上下文，请使用`Task`类中的`context`参数来指定应用作后续任务上下文的输出。

## 层级流程

模仿企业层级结构，CrewAI允许指定自定义管理器代理或自动创建一个管理器代理，需要指定管理器语言模型（`manager_llm`）。该代理监督任务执行，包括规划、分配和验证。任务不是预先分配的；管理器根据代理的能力分配任务，审查输出，并评估任务完成情况。

## Process类：详细概述

`Process`类实现为枚举（`Enum`），确保类型安全并将流程值限制为定义的类型（`sequential`、`hierarchical`）。共识流程计划未来包含在内，强调我们对持续发展和创新的承诺。

## 结论

CrewAI内流程所促进的结构化协作对于实现代理之间的系统性团队合作至关重要。
本文档已更新，以反映最新功能、增强功能以及共识流程的计划集成，确保用户能够获得最新和最全面的信息。