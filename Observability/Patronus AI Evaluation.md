# Patronus AI 评估

> 使用 Patronus AI 的全面评估平台监控和评估 CrewAI 智能体性能，评估 LLM 输出和智能体行为。

# Patronus AI 评估

## 概述

[Patronus AI](https://patronus.ai) 为 CrewAI 智能体提供全面的评估和监控功能，使您能够评估模型输出、智能体行为和整体系统性能。这种集成允许您实施持续评估工作流，帮助在生产环境中保持质量和可靠性。

## 主要功能

* **自动评估**：实时评估智能体输出和行为
* **自定义标准**：定义针对您的用例定制的特定评估标准
* **性能监控**：随时间跟踪智能体性能指标
* **质量保证**：确保在不同场景下输出质量的一致性
* **安全与合规**：监控潜在问题和政策违规

## 评估工具

Patronus 为不同用例提供了三种主要评估工具：

1. **PatronusEvalTool**：允许智能体为评估任务选择最合适的评估器和标准。
2. **PatronusPredefinedCriteriaEvalTool**：使用用户指定的预定义评估器和标准。
3. **PatronusLocalEvaluatorTool**：使用用户定义的自定义函数评估器。

## 安装

要使用这些工具，您需要安装 Patronus 包：

```shell  theme={null}
uv add patronus
```

您还需要将 Patronus API 密钥设置为环境变量：

```shell  theme={null}
export PATRONUS_API_KEY="your_patronus_api_key"
```

## 入门步骤

要有效使用 Patronus 评估工具，请按照以下步骤操作：

1. **安装 Patronus**：使用上述命令安装 Patronus 包。
2. **设置 API 密钥**：将您的 Patronus API 密钥设置为环境变量。
3. **选择合适的工具**：根据您的需求选择合适的 Patronus 评估工具。
4. **配置工具**：使用必要的参数配置工具。

## 示例

### 使用 PatronusEvalTool

以下示例演示了如何使用 `PatronusEvalTool`，该工具允许智能体选择最合适的评估器和标准：

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import PatronusEvalTool

# 初始化工具
patronus_eval_tool = PatronusEvalTool()

# 定义使用该工具的智能体
coding_agent = Agent(
    role="编程智能体",
    goal="生成高质量代码并验证输出是否为代码",
    backstory="一名能够生成高质量 Python 代码的资深程序员。",
    tools=[patronus_eval_tool],
    verbose=True,
)

# 生成和评估代码的示例任务
generate_code_task = Task(
    description="创建一个简单程序来生成斐波那契数列的前 N 个数字。为评估您的输出选择最合适的评估器和标准。",
    expected_output="生成斐波那契数列前 N 个数字的程序。",
    agent=coding_agent,
)

# 创建并运行团队
crew = Crew(agents=[coding_agent], tasks=[generate_code_task])
result = crew.kickoff()
```

### 使用 PatronusPredefinedCriteriaEvalTool

以下示例演示了如何使用 `PatronusPredefinedCriteriaEvalTool`，该工具使用预定义的评估器和标准：

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import PatronusPredefinedCriteriaEvalTool

# 使用预定义标准初始化工具
patronus_eval_tool = PatronusPredefinedCriteriaEvalTool(
    evaluators=[{"evaluator": "judge", "criteria": "contains-code"}]
)

# 定义使用该工具的智能体
coding_agent = Agent(
    role="编程智能体",
    goal="生成高质量代码",
    backstory="一名能够生成高质量 Python 代码的资深程序员。",
    tools=[patronus_eval_tool],
    verbose=True,
)

# 生成代码的示例任务
generate_code_task = Task(
    description="创建一个简单程序来生成斐波那契数列的前 N 个数字。",
    expected_output="生成斐波那契数列前 N 个数字的程序。",
    agent=coding_agent,
)

# 创建并运行团队
crew = Crew(agents=[coding_agent], tasks=[generate_code_task])
result = crew.kickoff()
```

### 使用 PatronusLocalEvaluatorTool

以下示例演示了如何使用 `PatronusLocalEvaluatorTool`，该工具使用自定义函数评估器：

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import PatronusLocalEvaluatorTool
from patronus import Client, EvaluationResult
import random

# 初始化 Patronus 客户端
client = Client()

# 注册自定义评估器
@client.register_local_evaluator("random_evaluator")
def random_evaluator(**kwargs):
    score = random.random()
    return EvaluationResult(
        score_raw=score,
        pass_=score >= 0.5,
        explanation="示例解释",
    )

# 使用自定义评估器初始化工具
patronus_eval_tool = PatronusLocalEvaluatorTool(
    patronus_client=client,
    evaluator="random_evaluator",
    evaluated_model_gold_answer="示例标签",
)

# 定义使用该工具的智能体
coding_agent = Agent(
    role="编程智能体",
    goal="生成高质量代码",
    backstory="一名能够生成高质量 Python 代码的资深程序员。",
    tools=[patronus_eval_tool],
    verbose=True,
)

# 生成代码的示例任务
generate_code_task = Task(
    description="创建一个简单程序来生成斐波那契数列的前 N 个数字。",
    expected_output="生成斐波那契数列前 N 个数字的程序。",
    agent=coding_agent,
)

# 创建并运行团队
crew = Crew(agents=[coding_agent], tasks=[generate_code_task])
result = crew.kickoff()
```

## 参数

### PatronusEvalTool

`PatronusEvalTool` 在初始化期间不需要任何参数。它会自动从 Patronus API 获取可用的评估器和标准。

### PatronusPredefinedCriteriaEvalTool

`PatronusPredefinedCriteriaEvalTool` 在初始化期间接受以下参数：

* **evaluators**：必需。包含要使用的评估器和标准的字典列表。例如：`[{"evaluator": "judge", "criteria": "contains-code"}]`。

### PatronusLocalEvaluatorTool

`PatronusLocalEvaluatorTool` 在初始化期间接受以下参数：

* **patronus\_client**：必需。Patronus 客户端实例。
* **evaluator**：可选。要使用的已注册本地评估器的名称。默认为空字符串。
* **evaluated\_model\_gold\_answer**：可选。用于评估的标准答案。默认为空字符串。

## 用法

使用 Patronus 评估工具时，您提供模型输入、输出和上下文，工具将返回来自 Patronus API 的评估结果。

对于 `PatronusEvalTool` 和 `PatronusPredefinedCriteriaEvalTool`，调用工具时需要以下参数：

* **evaluated\_model\_input**：智能体的任务描述（简单文本）。
* **evaluated\_model\_output**：智能体的任务输出。
* **evaluated\_model\_retrieved\_context**：智能体的上下文。

对于 `PatronusLocalEvaluatorTool`，需要相同的参数，但评估器和标准答案在初始化时指定。

## 结论

Patronus 评估工具提供了一种强大的方式，使用 Patronus AI 平台评估和评分模型输入和输出。通过使智能体能够评估自己的输出或其他智能体的输出，这些工具可以帮助提高 CrewAI 工作流的质量和可靠性。