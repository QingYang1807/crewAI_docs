# LangDB 集成

> 通过 LangDB AI 网关管理、保护和优化您的 CrewAI 工作流——访问 350+ 模型、自动路由、成本优化和全面的可观测性。

# 介绍

[LangDB AI 网关](https://langdb.ai)提供兼容 OpenAI 的 API，用于连接多个大型语言模型，并作为一个可观测性平台，能够轻松地端到端追踪 CrewAI 工作流，同时提供对 350+ 语言模型的访问。仅需一次 `init()` 调用，所有代理交互、任务执行和 LLM 调用都会被捕获，为您的应用程序提供全面的可观测性和生产就绪的 AI 基础设施。

<Frame caption="LangDB CrewAI 追踪示例">
  <img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langdb-1.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=6303d15055c19a7a6ec9d0c664f15c9f" alt="LangDB CrewAI 追踪示例" data-og-width="1576" width="1576" data-og-height="892" height="892" data-path="images/langdb-1.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langdb-1.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=da34c5dce0a82022f85feda11af459d4 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langdb-1.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=902f87462033e4874e3d373953158a39 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langdb-1.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=74c605bfb1a201c0f663d1c04b8daf60 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langdb-1.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=880a223bb40e9c94724c6047b7a2e966 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langdb-1.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=2c3dd2cac11506d6cc1aeba11c79759d 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langdb-1.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=a6d52809351409222de871b2f8018715 2500w" />
</Frame>

**查看：**[查看实时追踪示例](https://app.langdb.ai/sharing/threads/3becbfed-a1be-ae84-ea3c-4942867a3e22)

## 功能特性

### AI 网关能力

* **访问 350+ LLM**：通过单一集成连接所有主要语言模型
* **虚拟模型**：使用特定参数和路由规则创建自定义模型配置
* **虚拟 MCP**：启用与 MCP（模型上下文协议）系统的兼容性和集成，以增强代理通信
* **护栏**：为代理行为实施安全措施和合规控制

### 可观测性与追踪

* **自动追踪**：单次 `init()` 调用捕获所有 CrewAI 交互
* **端到端可见性**：从头到尾监控代理工作流
* **工具使用追踪**：跟踪代理使用的工具及其结果
* **模型调用监控**：LLM 交互的详细洞察
* **性能分析**：监控延迟、令牌使用量和成本
* **调试支持**：逐步执行以进行故障排除
* **实时监控**：实时追踪和指标仪表板

## 设置说明

<Steps>
  <Step title="安装 LangDB">
    使用 CrewAI 功能标志安装 LangDB 客户端：

    ```bash  theme={null}
    pip install 'pylangdb[crewai]'
    ```
  </Step>

  <Step title="设置环境变量">
    配置您的 LangDB 凭据：

    ```bash  theme={null}
    export LANGDB_API_KEY="<your_langdb_api_key>"
    export LANGDB_PROJECT_ID="<your_langdb_project_id>"
    export LANGDB_API_BASE_URL='https://api.us-east-1.langdb.ai'
    ```
  </Step>

  <Step title="初始化追踪">
    在配置 CrewAI 代码之前导入并初始化 LangDB：

    ```python  theme={null}
    from pylangdb.crewai import init
    # 初始化 LangDB
    init()
    ```
  </Step>

  <Step title="使用 LangDB 配置 CrewAI">
    使用 LangDB 标头设置您的 LLM：

    ```python  theme={null}
    from crewai import Agent, Task, Crew, LLM
    import os

    # 使用 LangDB 标头配置 LLM
    llm = LLM(
        model="openai/gpt-4o", # 替换为您想使用的模型
        api_key=os.getenv("LANGDB_API_KEY"),
        base_url=os.getenv("LANGDB_API_BASE_URL"),
        extra_headers={"x-project-id": os.getenv("LANGDB_PROJECT_ID")}
    )
    ```
  </Step>
</Steps>

## 快速开始示例

这是一个简单的示例，帮助您开始使用 LangDB 和 CrewAI：

```python  theme={null}
import os
from pylangdb.crewai import init
from crewai import Agent, Task, Crew, LLM

# 在任何 CrewAI 导入之前初始化 LangDB
init()

def create_llm(model):
    return LLM(
        model=model,
        api_key=os.environ.get("LANGDB_API_KEY"),
        base_url=os.environ.get("LANGDB_API_BASE_URL"),
        extra_headers={"x-project-id": os.environ.get("LANGDB_PROJECT_ID")}
    )

# 定义您的代理
researcher = Agent(
    role="Research Specialist",
    goal="Research topics thoroughly",
    backstory="Expert researcher with skills in finding information",
    llm=create_llm("openai/gpt-4o"), # 替换为您想使用的模型
    verbose=True
)

# 创建任务
task = Task(
    description="Research the given topic and provide a comprehensive summary",
    agent=researcher,
    expected_output="Detailed research summary with key findings"
)

# 创建并运行团队
crew = Crew(agents=[researcher], tasks=[task])
result = crew.kickoff()
print(result)
```

## 完整示例：研究与规划代理

这个全面的示例展示了具有研究和规划功能的多代理工作流。

### 先决条件

```bash  theme={null}
pip install crewai 'pylangdb[crewai]' crewai_tools setuptools python-dotenv
```

### 环境设置

```bash  theme={null}
# LangDB 凭据
export LANGDB_API_KEY="<your_langdb_api_key>"
export LANGDB_PROJECT_ID="<your_langdb_project_id>"
export LANGDB_API_BASE_URL='https://api.us-east-1.langdb.ai'

# 其他 API 密钥（可选）
export SERPER_API_KEY="<your_serper_api_key>"  # 用于网络搜索功能
```

### 完整实现

```python  theme={null}
#!/usr/bin/env python3

import os
import sys
from pylangdb.crewai import init
init()  # 在任何 CrewAI 导入之前初始化 LangDB
from dotenv import load_dotenv
from crewai import Agent, Task, Crew, Process, LLM
from crewai_tools import SerperDevTool

load_dotenv()

def create_llm(model):
    return LLM(
        model=model,
        api_key=os.environ.get("LANGDB_API_KEY"),
        base_url=os.environ.get("LANGDB_API_BASE_URL"),
        extra_headers={"x-project-id": os.environ.get("LANGDB_PROJECT_ID")}
    )

class ResearchPlanningCrew:
    def researcher(self) -> Agent:
        return Agent(
            role="Research Specialist",
            goal="Research topics thoroughly and compile comprehensive information",
            backstory="Expert researcher with skills in finding and analyzing information from various sources",
            tools=[SerperDevTool()],
            llm=create_llm("openai/gpt-4o"),
            verbose=True
        )
  
    def planner(self) -> Agent:
        return Agent(
            role="Strategic Planner",
            goal="Create actionable plans based on research findings",
            backstory="Strategic planner who breaks down complex challenges into executable plans",
            reasoning=True,
            max_reasoning_attempts=3,
            llm=create_llm("openai/anthropic/claude-3.7-sonnet"),
            verbose=True
        )
  
    def research_task(self) -> Task:
        return Task(
            description="Research the topic thoroughly and compile comprehensive information",
            agent=self.researcher(),
            expected_output="Comprehensive research report with key findings and insights"
        )
  
    def planning_task(self) -> Task:
        return Task(
            description="Create a strategic plan based on the research findings",
            agent=self.planner(),
            expected_output="Strategic execution plan with phases, goals, and actionable steps",
            context=[self.research_task()]
        )
  
    def crew(self) -> Crew:
        return Crew(
            agents=[self.researcher(), self.planner()],
            tasks=[self.research_task(), self.planning_task()],
            verbose=True,
            process=Process.sequential
        )

def main():
        topic = sys.argv[1] if len(sys.argv) > 1 else "Artificial Intelligence in Healthcare"
      
        crew_instance = ResearchPlanningCrew()
      
        # 使用特定主题更新任务描述
        crew_instance.research_task().description = f"Research {topic} thoroughly and compile comprehensive information"
    crew_instance.planning_task().description = f"Create a strategic plan for {topic} based on the research findings"
  
    result = crew_instance.crew().kickoff()
    print(result)

if __name__ == "__main__":
    main()
```

### 运行示例

```bash  theme={null}
python main.py "Sustainable Energy Solutions"
```

## 在 LangDB 中查看追踪

运行 CrewAI 应用程序后，您可以在 LangDB 仪表板中查看详细追踪：

<Frame caption="LangDB 追踪仪表板">
  <img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langdb-2.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=74a88afefce3f84fb6f9482a1790b413" alt="显示 CrewAI 工作流的 LangDB 追踪仪表板" data-og-width="1627" width="1627" data-og-height="890" height="890" data-path="images/langdb-2.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langdb-2.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=8e43fd482dc80800788d7092a8e51f1c 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langdb-2.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=02e4ddf9b1499b19d1e221628f5c21a4 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langdb-2.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=31e9c94a264d2b33fc9bfbb6a744dc15 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langdb-2.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=5de66fecea56248c974a91c918135e52 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langdb-2.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=e03d206dd7c466cc5b9de55bab46daf1 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langdb-2.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=4c16c2587ec6b48bf8cff71b9df17d07 2500w" />
</Frame>

### 您将看到的内容

* **代理交互**：代理对话和任务交接的完整流程
* **工具使用**：调用了哪些工具，它们的输入和输出
* **模型调用**：包含提示和响应的详细 LLM 交互
* **性能指标**：延迟、令牌使用量和成本跟踪
* **执行时间线**：整个工作流的逐步视图

## 故障排除

### 常见问题

* **没有出现追踪**：确保在任何 CrewAI 导入之前调用 `init()`
* **身份验证错误**：验证您的 LangDB API 密钥和项目 ID

## 资源

<CardGroup cols={3}>
  <Card title="LangDB 文档" icon="book" href="https://docs.langdb.ai">
    官方 LangDB 文档和指南
  </Card>

  <Card title="LangDB 指南" icon="graduation-cap" href="https://docs.langdb.ai/guides">
    构建 AI 代理的分步教程
  </Card>

  <Card title="GitHub 示例" icon="github" href="https://github.com/langdb/langdb-samples/tree/main/examples/crewai">
    完整的 CrewAI 集成示例
  </Card>

  <Card title="LangDB 仪表板" icon="chart-line" href="https://app.langdb.ai">
    访问您的追踪和分析
  </Card>

  <Card title="模型目录" icon="list" href="https://app.langdb.ai/models">
    浏览 350+ 可用语言模型
  </Card>

  <Card title="企业功能" icon="building" href="https://docs.langdb.ai/enterprise">
    自托管选项和企业能力
  </Card>
</CardGroup>

## 下一步

本指南介绍了将 LangDB AI 网关与 CrewAI 集成的基础知识。要进一步增强您的 AI 工作流，请探索：

* **虚拟模型**：使用路由策略创建自定义模型配置
* **护栏与安全**：实施内容过滤和合规控制
* **生产部署**：配置回退、重试和负载均衡

有关更多高级功能和用例，请访问 [LangDB 文档](https://docs.langdb.ai) 或探索 [模型目录](https://app.langdb.ai/models) 以发现所有可用模型。