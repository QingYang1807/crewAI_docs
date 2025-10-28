# Maxim 集成

> 开始对 Agent 进行监控、评估和观测

# Maxim 概述

Maxim AI 为您的 CrewAI 应用程序提供全面的 Agent 监控、评估和观测功能。通过 Maxim 的一行集成，您可以轻松地追踪和分析 Agent 交互、性能指标等。

## 功能特性

### 提示管理

Maxim 的提示管理功能让您能够为 CrewAI Agent 创建、组织和优化提示。您可以通过 Maxim 的 SDK 动态检索和应用受版本控制的提示，而无需硬编码指令。

<Tabs>
  <Tab title="提示游乐场">
    通过游乐场创建、优化、实验和部署您的提示。使用文件夹和版本组织您的提示，通过链接工具和上下文进行真实场景的实验，并基于自定义逻辑进行部署。

    您可以通过 [**配置模型**](https://www.getmaxim.ai/docs/introduction/quickstart/setting-up-workspace#add-model-api-keys) 轻松跨模型进行实验，并从提示游乐场顶部的下拉菜单中选择相关模型。

    <img src="https://raw.githubusercontent.com/akmadan/crewAI/docs_maxim_observability/docs/images/maxim_playground.png" />
  </Tab>

  <Tab title="提示版本">
    在团队构建 AI 应用程序时，实验的重要组成部分是对提示结构进行迭代。为了有效协作并清晰地组织您的更改，Maxim 允许进行提示版本控制和跨版本比较运行。

    <img src="https://raw.githubusercontent.com/akmadan/crewAI/docs_maxim_observability/docs/images/maxim_versions.png" />
  </Tab>

  <Tab title="提示比较">
    在发展您的 AI 应用程序时对提示进行迭代，需要跨模型、提示结构等进行实验。为了比较版本并就更改做出明智决策，比较游乐场提供了结果的并排视图。

    ## **为何使用提示比较？**

    提示比较功能将多个单独的提示合并到一个视图中，为各种工作流程提供了简化的方法：

    1.  **模型比较**：评估不同模型在同一提示上的性能。
    2.  **提示优化**：比较同一提示的不同版本，以确定最有效的表述。
    3.  **跨模型一致性**：确保同一提示在不同模型上输出一致。
    4.  **性能基准测试**：分析不同模型和提示的延迟、成本和 token 数量等指标。
  </Tab>
</Tabs>

### 观测与评估

Maxim AI 为您的 CrewAI Agent 提供全面的观测和评估功能，帮助您准确了解每次执行期间发生的情况。

<Tabs>
  <Tab title="Agent 追踪">
    轻松追踪 Agent 的完整生命周期，包括工具调用、Agent 轨迹和决策流。

    <img src="https://raw.githubusercontent.com/akmadan/crewAI/docs_maxim_observability/docs/images/maxim_agent_tracking.png" />
  </Tab>

  <Tab title="分析 + 评估">
    对完整追踪或单个节点运行详细评估，支持：

    *   多步交互和细粒度追踪分析
    *   会话级别评估
    *   用于真实世界测试的模拟

    <img src="https://raw.githubusercontent.com/akmadan/crewAI/docs_maxim_observability/docs/images/maxim_trace_eval.png" />

    <CardGroup cols={3}>
      <Card title="日志自动评估" icon="e" href="https://www.getmaxim.ai/docs/observe/how-to/evaluate-logs/auto-evaluation">
        <p>
          根据过滤器或采样，直接从 UI 自动评估捕获的日志。
        </p>
      </Card>

      <Card title="日志人工评估" icon="hand" href="https://www.getmaxim.ai/docs/observe/how-to/evaluate-logs/human-evaluation">
        <p>
          使用人工评估或评分来评估日志质量。
        </p>
      </Card>

      <Card title="节点级评估" icon="road" href="https://www.getmaxim.ai/docs/observe/how-to/evaluate-logs/node-level-evaluation">
        <p>
          评估追踪或日志的任何组件，以深入了解您的 Agent 行为。
        </p>
      </Card>
    </CardGroup>

    ***
  </Tab>

  <Tab title="告警">
    为**错误**、**成本**、**token 使用量**、**用户反馈**和**延迟**设置阈值，并通过 Slack 或 PagerDuty 获取实时告警。

    <img src="https://raw.githubusercontent.com/akmadan/crewAI/docs_maxim_observability/docs/images/maxim_alerts_1.png" />
  </Tab>

  <Tab title="仪表盘">
    轻松可视化随时间变化的追踪记录、使用指标、延迟和错误率。

    <img src="https://raw.githubusercontent.com/akmadan/crewAI/docs_maxim_observability/docs/images/maxim_dashboard_1.png" />
  </Tab>
</Tabs>

## 快速开始

### 前置条件

*   Python 版本 >= 3.10
*   一个 Maxim 账户（[在此注册](https://getmaxim.ai/)）
*   生成 Maxim API 密钥
*   一个 CrewAI 项目

### 安装

通过 pip 安装 Maxim SDK：

```python  theme={null}
pip install maxim-py
```

或者将其添加到您的 `requirements.txt` 文件中：

```
maxim-py
```

### 基础设置

### 1. 设置环境变量

```python  theme={null}
### 环境变量设置

# 在您的项目根目录创建一个 `.env` 文件：

# Maxim API 配置
MAXIM_API_KEY=your_api_key_here
MAXIM_LOG_REPO_ID=your_repo_id_here
```

### 2. 导入所需的包

```python  theme={null}
from crewai import Agent, Task, Crew, Process
from maxim import Maxim
from maxim.logger.crewai import instrument_crewai
```

### 3. 使用您的 API 密钥初始化 Maxim

```python {8} theme={null}
# 仅需一行代码即可为 CrewAI 添加监控
instrument_crewai(Maxim().logger())
```

### 4. 像往常一样创建并运行您的 CrewAI 应用程序

```python  theme={null}
# 创建您的 agent
researcher = Agent(
    role='Senior Research Analyst',
    goal='Uncover cutting-edge developments in AI',
    backstory="You are an expert researcher at a tech think tank...",
    verbose=True,
    llm=llm
)

# 定义任务
research_task = Task(
    description="Research the latest AI advancements...",
    expected_output="",
    agent=researcher
)

# 配置并运行 crew
crew = Crew(
    agents=[researcher],
    tasks=[research_task],
    verbose=True
)

try:
    result = crew.kickoff()
finally:
    maxim.cleanup()  # 确保即使发生错误也会执行清理操作
```

就这样！您所有的 CrewAI Agent 交互现在都将被记录，并可在您的 Maxim 仪表盘中查看。

查看此 Google Colab Notebook 以快速参考 - [Notebook](https://colab.research.google.com/drive/1ZKIZWsmgQQ46n8TH9zLsT1negKkJA6K8?usp=sharing)

## 查看您的追踪记录

运行 CrewAI 应用程序后：

1.  登录您的 [Maxim 仪表盘](https://app.getmaxim.ai/login)
2.  导航至您的仓库
3.  查看详细的 Agent 追踪记录，包括：

    *   Agent 对话
    *   工具使用模式
    *   性能指标
    *   成本分析

    <img src="https://raw.githubusercontent.com/akmadan/crewAI/docs_maxim_observability/docs/images/crewai_traces.gif" />

## 故障排除

### 常见问题

*   **未出现追踪记录**：确保您的 API 密钥和仓库 ID 是正确的。
*   确保您在运行 crew **之前** **调用了 `instrument_crewai()`**。这会正确初始化日志记录钩子。
*   在您的 `instrument_crewai()` 调用中设置 `debug=True`，以暴露任何内部错误：

    ```python  theme={null}
    instrument_crewai(logger, debug=True)
    ```
*   为您的 Agent 配置 `verbose=True` 以捕获详细的日志：

    ```python  theme={null}
    agent = CrewAgent(..., verbose=True)
    ```
*   再次检查 `instrument_crewai()` 是否在创建或执行 Agent **之前**被调用。这可能显而易见，但它是一个常见的疏忽。

## 资源

<CardGroup cols="3">
  <Card title="CrewAI 文档" icon="book" href="https://docs.crewai.com/">
    CrewAI 官方文档
  </Card>

  <Card title="Maxim 文档" icon="book" href="https://getmaxim.ai/docs">
    Maxim 官方文档
  </Card>

  <Card title="Maxim Github" icon="github" href="https://github.com/maximhq">
    Maxim Github
  </Card>
</CardGroup>