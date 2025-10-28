# Braintrust

> 用于CrewAI的Braintrust集成，支持OpenTelemetry追踪和评估

# Braintrust 集成

本指南演示了如何将 **Braintrust** 与 **CrewAI** 集成，使用OpenTelemetry进行全面的追踪和评估。通过本指南，您将能够追踪您的CrewAI代理，监控它们的性能，并使用Braintrust强大的可观测性平台评估它们的输出。

> **什么是Braintrust？** [Braintrust](https://www.braintrust.dev) 是一个AI评估和可观测性平台，为AI应用程序提供全面的追踪、评估和监控功能，内置实验跟踪和性能分析。

## 开始使用

我们将通过一个简单示例来演示如何使用CrewAI，并通过OpenTelemetry将其与Braintrust集成，以实现全面的可观测性和评估。

### 第1步：安装依赖

```bash  theme={null}
uv add braintrust[otel] crewai crewai-tools opentelemetry-instrumentation-openai opentelemetry-instrumentation-crewai python-dotenv
```

### 第2步：设置环境变量

设置Braintrust API密钥并配置OpenTelemetry将追踪数据发送到Braintrust。您需要一个Braintrust API密钥和您的OpenAI API密钥。

```python  theme={null}
import os
from getpass import getpass

# 获取您的Braintrust凭据
BRAINTRUST_API_KEY = getpass("🔑 输入您的Braintrust API密钥: ")

# 获取服务的API密钥
OPENAI_API_KEY = getpass("🔑 输入您的OpenAI API密钥: ")

# 设置环境变量
os.environ["BRAINTRUST_API_KEY"] = BRAINTRUST_API_KEY
os.environ["BRAINTRUST_PARENT"] = "project_name:crewai-demo"
os.environ["OPENAI_API_KEY"] = OPENAI_API_KEY
```

### 第3步：使用Braintrust初始化OpenTelemetry

初始化Braintrust OpenTelemetry检测功能，开始捕获追踪数据并将其发送到Braintrust。

```python  theme={null}
import os
from typing import Any, Dict

from braintrust.otel import BraintrustSpanProcessor
from crewai import Agent, Crew, Task
from crewai.llm import LLM
from opentelemetry import trace
from opentelemetry.instrumentation.crewai import CrewAIInstrumentor
from opentelemetry.instrumentation.openai import OpenAIInstrumentor
from opentelemetry.sdk.trace import TracerProvider

def setup_tracing() -> None:
    """使用Braintrust设置OpenTelemetry追踪。"""
    current_provider = trace.get_tracer_provider()
    if isinstance(current_provider, TracerProvider):
        provider = current_provider
    else:
        provider = TracerProvider()
        trace.set_tracer_provider(provider)

    provider.add_span_processor(BraintrustSpanProcessor())
    CrewAIInstrumentor().instrument(tracer_provider=provider)
    OpenAIInstrumentor().instrument(tracer_provider=provider)


setup_tracing()
```

### 第4步：创建CrewAI应用程序

我们将创建一个CrewAI应用程序，其中两个代理协作研究和撰写关于AI进展的博客文章，并启用全面追踪。

```python  theme={null}
from crewai import Agent, Crew, Process, Task
from crewai_tools import SerperDevTool

def create_crew() -> Crew:
    """创建一个包含多个代理的团队以进行全面追踪。"""
    llm = LLM(model="gpt-4o-mini")
    search_tool = SerperDevTool()

    # 定义具有特定角色的代理
    researcher = Agent(
        role="高级研究分析师",
        goal="发现AI和数据科学的前沿发展",
        backstory="""你在一家领先的技术智库工作。
        你的专长在于识别新兴趋势。
        你擅长剖析复杂数据并提出可行的见解。""",
        verbose=True,
        allow_delegation=False,
        llm=llm,
        tools=[search_tool],
    )

    writer = Agent(
        role="技术内容策略师",
        goal="撰写关于技术进展的引人入胜的内容",
        backstory="""你是一位知名的内容策略师，以深刻见解和引人入胜的文章而闻名。
        你能将复杂的概念转化为引人入胜的叙事。""",
        verbose=True,
        allow_delegation=True,
        llm=llm,
    )

    # 为您的代理创建任务
    research_task = Task(
        description="""对{topic}的最新进展进行全面分析。
        识别关键趋势、突破性技术和潜在的行业影响。""",
        expected_output="以项目符号表示的完整分析报告",
        agent=researcher,
    )

    writing_task = Task(
        description="""利用提供的见解，撰写一篇引人入胜的博客
        文章，重点介绍最重要的{topic}进展。
        你的文章应该信息丰富且易于理解，面向精通技术的受众。
        让文章听起来很酷，避免使用复杂的词汇，以免听起来像AI生成的。""",
        expected_output="至少4段的完整博客文章",
        agent=writer,
        context=[research_task],
    )

    # 使用顺序流程实例化您的团队
    crew = Crew(
        agents=[researcher, writer], 
        tasks=[research_task, writing_task], 
        verbose=True, 
        process=Process.sequential
    )

    return crew

def run_crew():
    """运行团队并返回结果。"""
    crew = create_crew()
    result = crew.kickoff(inputs={"topic": "AI developments"})
    return result

# 运行您的团队
if __name__ == "__main__":
    # 检测功能已在此模块上方初始化
    result = run_crew()
    print(result)
```

### 第5步：在Braintrust中查看追踪数据

运行团队后，您可以通过不同的视角在Braintrust中查看全面的追踪数据：

<Tabs>
  <Tab title="追踪">
    <Frame>
      <img src="https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-trace-view.png?fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=311f727eeadf1c39380c08e992278dd0" alt="Braintrust Trace View" data-og-width="1446" width="1446" data-og-height="1117" height="1117" data-path="images/braintrust-trace-view.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-trace-view.png?w=280&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=897e860f9b8d3493999f62a9a19c8fb8 280w, https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-trace-view.png?w=560&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=a5f185d667eac4272b983878a6851206 560w, https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-trace-view.png?w=840&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=a633e5f94ed2b81419c17894d803342a 840w, https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-trace-view.png?w=1100&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=941454a8d9339cb1873525af08a3278c 1100w, https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-trace-view.png?w=1650&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=9617559998fb4906d5c81d7a77803077 1650w, https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-trace-view.png?w=2500&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=413a16f22e69897e63d899ac77601ab7 2500w" />
    </Frame>
  </Tab>

  <Tab title="时间线">
    <Frame>
      <img src="https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-timeline-view.png?fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=03090206aecd3a7b2f21a24af2514b08" alt="Braintrust Timeline View" data-og-width="1449" width="1449" data-og-height="950" height="950" data-path="images/braintrust-timeline-view.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-timeline-view.png?w=280&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=0795dce3045c51b3f954282196e21189 280w, https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-timeline-view.png?w=560&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=73c0b619f3b04b19b0efb255157a4ef5 560w, https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-timeline-view.png?w=840&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=e28b62d2fb6da5cc43c770796a1cf3ca 840w, https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-timeline-view.png?w=1100&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=7ccb443569f6a8e9bd6ae267fd0d83b3 1100w, https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-timeline-view.png?w=1650&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=94b8fecd6f45755faf0d6ced848a7da1 1650w, https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-timeline-view.png?w=2500&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=0fb8a89b44b1c5bcc16886d3bd61f8f0 2500w" />
    </Frame>
  </Tab>

  <Tab title="线程">
    <Frame>
      <img src="https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-thread-view.png?fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=1bc0a6842a5dd9e6d7c2dd742417e79b" alt="Braintrust Thread View" data-og-width="1452" width="1452" data-og-height="989" height="989" data-path="images/braintrust-thread-view.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-thread-view.png?w=280&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=1a7d95a7eeee928ea1ee0e84d4f172f7 280w, https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-thread-view.png?w=560&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=8414267a22466d638441e87588c0c3b5 560w, https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-thread-view.png?w=840&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=7695d8fe70972f93f69201a7f9e4f19f 840w, https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-thread-view.png?w=1100&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=2d5051293c140b63ae391cd0879aa313 1100w, https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-thread-view.png?w=1650&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=69bf97bbeb0e0b1a323cc535666d0517 1650w, https://mintcdn.com/crewai/sTI-JqU6hicMPzD1/images/braintrust-thread-view.png?w=2500&fit=max&auto=format&n=sTI-JqU6hicMPzD1&q=85&s=e0b50e3d14fad48ca9a48795c3d36bc9 2500w" />
    </Frame>
  </Tab>
</Tabs>

### 第6步：通过SDK进行评估（实验）

您还可以使用Braintrust的Eval SDK运行评估。这对于比较版本或离线评分输出很有用。以下是使用上面创建的团队的`Eval`类的Python示例：

```python  theme={null}
# eval_crew.py
from braintrust import Eval
from autoevals import Levenshtein

def evaluate_crew_task(input_data):
    """包装我们的团队以进行评估的任务函数。"""
    crew = create_crew()
    result = crew.kickoff(inputs={"topic": input_data["topic"]})
    return str(result)

Eval(
    "AI研究团队",  # 项目名称
    {
        "data": lambda: [
            {"topic": "2024年人工智能趋势"},
            {"topic": "机器学习突破"},
            {"topic": "AI伦理与治理"},
        ],
        "task": evaluate_crew_task,
        "scores": [Levenshtein],
    },
)
```

设置您的API密钥并运行：

```bash  theme={null}
export BRAINTRUST_API_KEY="YOUR_API_KEY"
braintrust eval eval_crew.py
```

有关更多详细信息，请参阅[Braintrust Eval SDK指南](https://www.braintrust.dev/docs/start/eval-sdk)。

### Braintrust集成的关键特性

* **全面追踪**：跟踪所有代理交互、工具使用和LLM调用
* **性能监控**：监控执行时间、令牌使用和成功率
* **实验跟踪**：比较不同的团队配置和模型
* **自动化评估**：为团队输出设置自定义评估指标
* **错误跟踪**：监控和调试团队执行中的失败情况
* **成本分析**：跟踪令牌使用和相关成本

### 版本兼容性信息

* Python 3.8+
* CrewAI >= 0.86.0
* Braintrust >= 0.1.0
* OpenTelemetry SDK >= 1.31.0

### 参考资料

* [Braintrust文档](https://www.braintrust.dev/docs) - Braintrust平台概览
* [Braintrust CrewAI集成](https://www.braintrust.dev/docs/integrations/crew-ai) - 官方CrewAI集成指南
* [Braintrust Eval SDK](https://www.braintrust.dev/docs/start/eval-sdk) - 通过SDK运行实验
* [CrewAI文档](https://docs.crewai.com/) - CrewAI框架概览
* [OpenTelemetry文档](https://opentelemetry.io/docs/) - OpenTelemetry指南
* [Braintrust GitHub](https://github.com/braintrustdata/braintrust) - Braintrust SDK源代码