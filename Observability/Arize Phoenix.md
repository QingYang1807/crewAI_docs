# Arize Phoenix

> 通过 OpenTelemetry 和 OpenInference 将 Arize Phoenix 与 CrewAI 集成

# Arize Phoenix 集成

本指南演示如何通过 [OpenInference](https://github.com/openinference/openinference) SDK 使用 OpenTelemetry 将 **Arize Phoenix** 与 **CrewAI** 集成。通过本指南，您将能够追踪您的 CrewAI 智能体并轻松调试它们。

> **什么是 Arize Phoenix？** [Arize Phoenix](https://phoenix.arize.com) 是一个 LLM 可观测性平台，为 AI 应用程序提供追踪和评估功能。

[![观看我们与 Phoenix 集成的视频演示](https://storage.googleapis.com/arize-assets/fixtures/setup_crewai.png)](https://www.youtube.com/watch?v=Yc5q3l6F7Ww)

## 入门指南

我们将通过一个简单示例，演示如何使用 CrewAI 并通过 OpenInference 使用 OpenTelemetry 将其与 Arize Phoenix 集成。

您也可以在 [Google Colab](https://colab.research.google.com/github/Arize-ai/phoenix/blob/main/tutorials/tracing/crewai_tracing_tutorial.ipynb) 上访问本指南。

### 第 1 步：安装依赖项

```bash  theme={null}
pip install openinference-instrumentation-crewai crewai crewai-tools arize-phoenix-otel
```

### 第 2 步：设置环境变量

设置 Phoenix Cloud API 密钥并配置 OpenTelemetry 以将追踪数据发送到 Phoenix。Phoenix Cloud 是 Arize Phoenix 的托管版本，但使用此集成并不需要它。

您可以[在这里](https://serper.dev/)获取免费的 Serper API 密钥。

```python  theme={null}
import os
from getpass import getpass

# 获取您的 Phoenix Cloud 凭据
PHOENIX_API_KEY = getpass("🔑 输入您的 Phoenix Cloud API 密钥: ")

# 获取服务的 API 密钥
OPENAI_API_KEY = getpass("🔑 输入您的 OpenAI API 密钥: ")
SERPER_API_KEY = getpass("🔑 输入您的 Serper API 密钥: ")

# 设置环境变量
os.environ["PHOENIX_CLIENT_HEADERS"] = f"api_key={PHOENIX_API_KEY}"
os.environ["PHOENIX_COLLECTOR_ENDPOINT"] = "https://app.phoenix.arize.com" # Phoenix Cloud，如果您使用自托管实例，请将其更改为您自己的端点
os.environ["OPENAI_API_KEY"] = OPENAI_API_KEY
os.environ["SERPER_API_KEY"] = SERPER_API_KEY
```

### 第 3 步：使用 Phoenix 初始化 OpenTelemetry

初始化 OpenInference OpenTelemetry 检测 SDK，开始捕获追踪数据并将其发送到 Phoenix。

```python  theme={null}
from phoenix.otel import register

tracer_provider = register(
    project_name="crewai-tracing-demo",
    auto_instrument=True,
)
```

### 第 4 步：创建 CrewAI 应用程序

我们将创建一个 CrewAI 应用程序，其中两个智能体协作研究和撰写关于 AI 进展的博客文章。

```python  theme={null}
from crewai import Agent, Crew, Process, Task
from crewai_tools import SerperDevTool
from openinference.instrumentation.crewai import CrewAIInstrumentor
from phoenix.otel import register

# 为您的团队设置监控
tracer_provider = register(
    endpoint="http://localhost:6006/v1/traces")
CrewAIInstrumentor().instrument(skip_dep_check=True, tracer_provider=tracer_provider)
search_tool = SerperDevTool()

# 定义您的智能体及其角色和目标
researcher = Agent(
    role="高级研究分析师",
    goal="揭示人工智能和数据科学的前沿发展",
    backstory="""您在一家领先的科技智库工作。
    您的专业在于识别新兴趋势。
    您善于剖析复杂数据并提出可行的见解。""",
    verbose=True,
    allow_delegation=False,
    # 您可以传递一个可选的 llm 属性，指定您想要使用的模型。
    # llm=ChatOpenAI(model_name="gpt-3.5", temperature=0.7),
    tools=[search_tool],
)
writer = Agent(
    role="技术内容策略师",
    goal="撰写关于技术进步的引人入胜的内容",
    backstory="""您是一位著名的内容策略师，以其富有洞察力和吸引力的文章而闻名。
    您将复杂的概念转化为引人入胜的叙事。""",
    verbose=True,
    allow_delegation=True,
)

# 为您的智能体创建任务
task1 = Task(
    description="""对 2024 年 AI 的最新进展进行全面分析。
    识别关键趋势、突破性技术和潜在的行业影响。""",
    expected_output="以要点形式呈现的完整分析报告",
    agent=researcher,
)

task2 = Task(
    description="""利用提供的见解，撰写一篇引人入胜的博客文章，
    重点介绍最重要的 AI 进展。
    您的文章应该既有信息量又易于理解，面向精通技术的受众。
    使其听起来很酷，避免复杂的词汇，使其不像 AI 写的。""",
    expected_output="至少四段的完整博客文章",
    agent=writer,
)

# 使用顺序流程实例化您的团队
crew = Crew(
    agents=[researcher, writer], tasks=[task1, task2], verbose=1, process=Process.sequential
)

# 让您的团队开始工作！
result = crew.kickoff()

print("######################")
print(result)
```

### 第 5 步：在 Phoenix 中查看追踪

运行智能体后，您可以在 Phoenix 中查看 CrewAI 应用程序生成的追踪。您应该看到智能体交互和 LLM 调用的详细步骤，这可以帮助您调试和优化 AI 智能体。

登录您的 Phoenix Cloud 帐户并导航到您在 `project_name` 参数中指定的项目。您将看到一个包含所有智能体交互、工具使用和 LLM 调用的追踪时间线视图。

![Phoenix 中显示智能体交互的追踪示例](https://storage.googleapis.com/arize-assets/fixtures/crewai_traces.png)

### 版本兼容性信息

* Python 3.8+
* CrewAI >= 0.86.0
* Arize Phoenix >= 7.0.1
* OpenTelemetry SDK >= 1.31.0

### 参考资料

* [Phoenix 文档](https://docs.arize.com/phoenix/) - Phoenix 平台概述。
* [CrewAI 文档](https://docs.crewai.com/) - CrewAI 框架概述。
* [OpenTelemetry 文档](https://opentelemetry.io/docs/) - OpenTelemetry 指南。
* [OpenInference GitHub](https://github.com/openinference/openinference) - OpenInference SDK 的源代码。