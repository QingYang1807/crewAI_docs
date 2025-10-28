# Datadog 集成

> 了解如何将 Datadog 与 CrewAI 集成，以便将 LLM 可观测性跟踪数据提交到 Datadog。

# 将 Datadog 与 CrewAI 集成

本指南将演示如何使用 [Datadog 自动检测](https://docs.datadoghq.com/llm_observability/instrumentation/auto_instrumentation?tab=python) 将 **[Datadog LLM 可观测性](https://docs.datadoghq.com/llm_observability/)** 与 **CrewAI** 集成。在本指南结束时，您将能够将 LLM 可观测性跟踪数据提交到 Datadog，并在 Datadog LLM 可观测性的[代理执行视图](https://docs.datadoghq.com/llm_observability/monitoring/agent_monitoring)中查看您的 CrewAI 代理运行情况。

## 什么是 Datadog LLM 可观测性？

[Datadog LLM 可观测性](https://www.datadoghq.com/product/llm-observability/)帮助 AI 工程师、数据科学家和应用程序开发人员快速开发、评估和监控 LLM 应用程序。通过结构化实验、跨 AI 代理的端到端跟踪以及评估，有信心地提高输出质量、性能、成本和整体风险。

## 入门指南

### 安装依赖项

```shell  theme={null}
pip install ddtrace crewai crewai-tools
```

### 设置环境变量

如果您没有 Datadog API 密钥，可以[创建账户](https://www.datadoghq.com/)并[获取您的 API 密钥](https://docs.datadoghq.com/account_management/api-app-keys/#api-keys)。

您还需要在以下环境变量中指定一个 ML 应用程序名称。ML 应用程序是与特定基于 LLM 的应用程序相关联的 LLM 可观测性跟踪数据的分组。有关 ML 应用程序名称限制的更多信息，请参阅[ML 应用程序命名指南](https://docs.datadoghq.com/llm_observability/instrumentation/sdk?tab=python#application-naming-guidelines)。

```shell  theme={null}
export DD_API_KEY=<YOUR_DD_API_KEY>
export DD_SITE=<YOUR_DD_SITE>
export DD_LLMOBS_ENABLED=true
export DD_LLMOBS_ML_APP=<YOUR_ML_APP_NAME>
export DD_LLMOBS_AGENTLESS_ENABLED=true
export DD_APM_TRACING_ENABLED=false
```

此外，配置任何 LLM 提供商的 API 密钥

```shell  theme={null}
export OPENAI_API_KEY=<YOUR_OPENAI_API_KEY>
export ANTHROPIC_API_KEY=<YOUR_ANTHROPIC_API_KEY>
export GEMINI_API_KEY=<YOUR_GEMINI_API_KEY>
...
```

### 创建 CrewAI 代理应用程序

```python  theme={null}
# crewai_agent.py
from crewai import Agent, Task, Crew

from crewai_tools import (
    WebsiteSearchTool
)

web_rag_tool = WebsiteSearchTool()

writer = Agent(
    role="Writer",
    goal="You make math engaging and understandable for young children through poetry",
    backstory="You're an expert in writing haikus but you know nothing of math.",
    tools=[web_rag_tool],
)

task = Task(
    description=("What is {multiplication}?"),
    expected_output=("Compose a haiku that includes the answer."),
    agent=writer
)

crew = Crew(
    agents=[writer],
    tasks=[task],
    share_crew=False
)

output = crew.kickoff(dict(multiplication="2 * 2"))
```

### 使用 Datadog 自动检测运行应用程序

设置了[环境变量](#set-environment-variables)后，您现在可以使用 Datadog 自动检测运行应用程序。

```shell  theme={null}
ddtrace-run python crewai_agent.py
```

### 在 Datadog 中查看跟踪数据

运行应用程序后，您可以在[Datadog LLM 可观测性的跟踪视图](https://app.datadoghq.com/llm/traces)中查看跟踪数据，从左上角的下拉菜单中选择您选择的 ML 应用程序名称。

单击跟踪将显示跟踪的详细信息，包括使用的总令牌数、LLM 调用次数、使用的模型和预估成本。单击特定的 span 将缩小这些详细信息的范围，并显示相关的输入、输出和元数据。

<Frame>
  <img src="https://mintcdn.com/crewai/YHPIyIjbLg-0Yd_V/images/datadog-llm-observability-1.png?fit=max&auto=format&n=YHPIyIjbLg-0Yd_V&q=85&s=12b76bf2631c8b24ed6828053b125074" alt="Datadog LLM 可观测性跟踪视图" data-og-width="2678" width="2678" data-og-height="1572" height="1572" data-path="images/datadog-llm-observability-1.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/YHPIyIjbLg-0Yd_V/images/datadog-llm-observability-1.png?w=280&fit=max&auto=format&n=YHPIyIjbLg-0Yd_V&q=85&s=1862b54dec692db71de36087c425b3e6 280w, https://mintcdn.com/crewai/YHPIyIjbLg-0Yd_V/images/datadog-llm-observability-1.png?w=560&fit=max&auto=format&n=YHPIyIjbLg-0Yd_V&q=85&s=a3d32811584412edba3c66c1024c28d2 560w, https://mintcdn.com/crewai/YHPIyIjbLg-0Yd_V/images/datadog-llm-observability-1.png?w=840&fit=max&auto=format&n=YHPIyIjbLg-0Yd_V&q=85&s=fc6ed7c46b781e9c4a6f393150c99399 840w, https://mintcdn.com/crewai/YHPIyIjbLg-0Yd_V/images/datadog-llm-observability-1.png?w=1100&fit=max&auto=format&n=YHPIyIjbLg-0Yd_V&q=85&s=2488ea5cbb8b2b852f03efa9f58de19c 1100w, https://mintcdn.com/crewai/YHPIyIjbLg-0Yd_V/images/datadog-llm-observability-1.png?w=1650&fit=max&auto=format&n=YHPIyIjbLg-0Yd_V&q=85&s=9f541b37cbd11c65251eeaf2e91259aa 1650w, https://mintcdn.com/crewai/YHPIyIjbLg-0Yd_V/images/datadog-llm-observability-1.png?w=2500&fit=max&auto=format&n=YHPIyIjbLg-0Yd_V&q=85&s=31fb1902fd6a4c3703167674644f4a8a 2500w" />
</Frame>

此外，您还可以查看跟踪的执行图视图，该视图显示跟踪的控制流和数据流，它将随着更大的代理扩展以显示 LLM 调用、工具调用和代理交互之间的交接和关系。

<Frame>
  <img src="https://mintcdn.com/crewai/YHPIyIjbLg-0Yd_V/images/datadog-llm-observability-2.png?fit=max&auto=format&n=YHPIyIjbLg-0Yd_V&q=85&s=01a94c3f9f162ce0b918fa6039f6fa8e" alt="Datadog LLM 可观测性代理执行流视图" data-og-width="4774" width="4774" data-og-height="2566" height="2566" data-path="images/datadog-llm-observability-2.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/YHPIyIjbLg-0Yd_V/images/datadog-llm-observability-2.png?w=280&fit=max&auto=format&n=YHPIyIjbLg-0Yd_V&q=85&s=68d556845570e0aa0df070d1de7067ce 280w, https://mintcdn.com/crewai/YHPIyIjbLg-0Yd_V/images/datadog-llm-observability-2.png?w=560&fit=max&auto=format&n=YHPIyIjbLg-0Yd_V&q=85&s=f5b67e3fb8beb9acd769042e188d04a6 560w, https://mintcdn.com/crewai/YHPIyIjbLg-0Yd_V/images/datadog-llm-observability-2.png?w=840&fit=max&auto=format&n=YHPIyIjbLg-0Yd_V&q=85&s=3d045e43cc2846acaae17fd0d741ef90 840w, https://mintcdn.com/crewai/YHPIyIjbLg-0Yd_V/images/datadog-llm-observability-2.png?w=1100&fit=max&auto=format&n=YHPIyIjbLg-0Yd_V&q=85&s=b1802677a2e3403247a3b789c65cf9b6 1100w, https://mintcdn.com/crewai/YHPIyIjbLg-0Yd_V/images/datadog-llm-observability-2.png?w=1650&fit=max&auto=format&n=YHPIyIjbLg-0Yd_V&q=85&s=9bbcb20ebbad420307e1f4cdb836c1c8 1650w, https://mintcdn.com/crewai/YHPIyIjbLg-0Yd_V/images/datadog-llm-observability-2.png?w=2500&fit=max&auto=format&n=YHPIyIjbLg-0Yd_V&q=85&s=2d98c2f8224f78bfe8bc2a3ee99464f1 2500w" />
</Frame>

## 参考资料

* [Datadog LLM 可观测性](https://www.datadoghq.com/product/llm-observability/)
* [Datadog LLM 可观测性 CrewAI 自动检测](https://docs.datadoghq.com/llm_observability/instrumentation/auto_instrumentation?tab=python#crew-ai)