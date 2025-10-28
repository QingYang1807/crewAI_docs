# CrewAI 追踪功能

> 使用 CrewAI AMP 平台为 CrewAI 的 Crews 和 Flows 提供内置追踪功能

# CrewAI 内置追踪功能

CrewAI 提供内置追踪功能，使您能够实时监控和调试您的 Crews 和 Flows。本指南演示如何使用 CrewAI 的集成可观测性平台为 **Crews** 和 **Flows** 启用追踪。

> **什么是 CrewAI 追踪功能？** CrewAI 的内置追踪功能为您的 AI 代理提供全面的可观测性，包括代理决策、任务执行时间线、工具使用情况和 LLM 调用 - 所有这些都可以通过 [CrewAI AMP 平台](https://app.crewai.com) 访问。

## 先决条件

在使用 CrewAI 追踪功能之前，您需要：

1. **CrewAI AMP 账户**：在 [app.crewai.com](https://app.crewai.com) 注册免费账户
2. **CLI 身份验证**：使用 CrewAI CLI 对您的本地环境进行身份验证

```bash  theme={null}
crewai login
```

## 设置说明

### 第 1 步：创建您的 CrewAI AMP 账户

访问 [app.crewai.com](https://app.crewai.com) 并创建您的免费账户。这将使您能够访问 CrewAI AMP 平台，在那里您可以查看追踪、指标并管理您的 crews。

### 第 2 步：安装 CrewAI CLI 并进行身份验证

如果尚未安装，请使用 CLI 工具安装 CrewAI：

```bash  theme={null}
uv add crewai[tools]
```

然后使用您的 CrewAI AMP 账户对您的 CLI 进行身份验证：

```bash  theme={null}
crewai login
```

此命令将：

1. 在浏览器中打开身份验证页面
2. 提示您输入设备代码
3. 使用您的 CrewAI AMP 账户对您的本地环境进行身份验证
4. 为您的本地开发启用追踪功能

### 第 3 步：在您的 Crew 中启用追踪

您可以通过将 `tracing` 参数设置为 `True` 来为您的 Crew 启用追踪：

```python  theme={null}
from crewai import Agent, Crew, Process, Task
from crewai_tools import SerperDevTool

# 定义您的代理
researcher = Agent(
    role="高级研究分析师",
    goal="发现 AI 和数据科学领域的前沿发展",
    backstory="""您在一家领先的科技智囊团工作。
    您的专业在于识别新兴趋势。
    您擅长剖析复杂数据并提供可行的见解。""",
    verbose=True,
    tools=[SerperDevTool()],
)

writer = Agent(
    role="技术内容策略师",
    goal="撰写关于技术进步的引人入胜的内容",
    backstory="""您是一位著名的内容策略师，以富有洞察力和吸引力的文章而闻名。
    您能将复杂的概念转化为引人入胜的叙事。""",
    verbose=True,
)

# 为您的代理创建任务
research_task = Task(
    description="""对 2024 年 AI 的最新进展进行全面分析。
    确定关键趋势、突破性技术和潜在的行业影响。""",
    expected_output="以要点形式呈现的完整分析报告",
    agent=researcher,
)

writing_task = Task(
    description="""利用提供的见解，撰写一篇引人入胜的博客文章，
    重点介绍最重要的 AI 进展。
    您的文章应该既有信息量又易于理解，面向精通技术的受众。""",
    expected_output="至少四段的完整博客文章",
    agent=writer,
)

# 在您的 crew 中启用追踪
crew = Crew(
    agents=[researcher, writer],
    tasks=[research_task, writing_task],
    process=Process.sequential,
    tracing=True,  # 启用内置追踪
    verbose=True
)

# 执行您的 crew
result = crew.kickoff()
```

### 第 4 步：在您的 Flow 中启用追踪

同样，您可以为 CrewAI Flows 启用追踪：

```python  theme={null}
from crewai.flow.flow import Flow, listen, start
from pydantic import BaseModel

class ExampleState(BaseModel):
    counter: int = 0
    message: str = ""

class ExampleFlow(Flow[ExampleState]):
    def __init__(self):
        super().__init__(tracing=True)  # 为 flow 启用追踪

    @start()
    def first_method(self):
        print("启动 flow")
        self.state.counter = 1
        self.state.message = "Flow 已启动"
        return "continue"

    @listen("continue")
    def second_method(self):
        print("继续 flow")
        self.state.counter += 1
        self.state.message = "Flow 已继续"
        return "finish"

    @listen("finish")
    def final_method(self):
        print("完成 flow")
        self.state.counter += 1
        self.state.message = "Flow 已完成"

# 创建并运行启用追踪的 flow
flow = ExampleFlow(tracing=True)
result = flow.kickoff()
```

### 第 5 步：在 CrewAI AMP 仪表板中查看追踪

运行 crew 或 flow 后，您可以在 CrewAI AMP 仪表板中查看由您的 CrewAI 应用程序生成的追踪。您应该看到代理交互、工具使用和 LLM 调用的详细步骤。
只需点击下面的链接查看追踪，或前往仪表板中的追踪标签页[此处](https://app.crewai.com/crewai_plus/trace_batches)

### 替代方案：环境变量配置

您还可以通过设置环境变量来全局启用追踪：

```bash  theme={null}
export CREWAI_TRACING_ENABLED=true
```

或将其添加到您的 `.env` 文件中：

```env  theme={null}
CREWAI_TRACING_ENABLED=true
```

当设置此环境变量时，所有 Crews 和 Flows 将自动启用追踪，即使没有明确设置 `tracing=True`。

## 查看您的追踪

### 访问 CrewAI AMP 仪表板

1. 访问 [app.crewai.com](https://app.crewai.com) 并登录您的账户
2. 导航到您的项目仪表板
3. 点击 **Traces** 标签页查看执行详情

### 您在追踪中将看到的内容

CrewAI 追踪提供对以下内容的全面可见性：

* **代理决策**：查看代理如何推理任务并做出决策
* **任务执行时间线**：任务序列和依赖关系的可视化表示
* **工具使用**：监控调用了哪些工具及其结果
* **LLM 调用**：跟踪所有语言模型交互，包括提示和响应
* **性能指标**：执行时间、令牌使用情况和成本
* **错误追踪**：详细的错误信息和堆栈跟踪

### 追踪功能

* **执行时间线**：点击执行的不同阶段
* **详细日志**：访问全面的日志以进行调试
* **性能分析**：分析执行模式并优化性能
* **导出功能**：下载追踪以进行进一步分析

### 身份验证问题

如果您遇到身份验证问题：

1. 确保您已登录：`crewai login`
2. 检查您的互联网连接
3. 在 [app.crewai.com](https://app.crewai.com) 验证您的账户

### 追踪未显示

如果追踪未在仪表板中显示：

1. 确认在您的 Crew/Flow 中设置了 `tracing=True`
2. 如果使用环境变量，请检查 `CREWAI_TRACING_ENABLED=true` 是否已设置
3. 确保您已使用 `crewai login` 进行身份验证
4. 验证您的 crew/flow 是否正在实际执行