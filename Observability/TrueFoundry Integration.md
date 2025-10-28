# TrueFoundry 集成

TrueFoundry 提供一个企业级就绪的 [AI 网关](https://www.truefoundry.com/ai-gateway)，可以与像 CrewAI 这样的智能体框架集成，并为您的 AI 应用程序提供治理和可观测性。TrueFoundry AI 网关作为 LLM 访问的统一接口，提供以下功能：

* **统一 API 访问**：通过一个 API 连接到 250+ 个 LLM（OpenAI、Claude、Gemini、Groq、Mistral）
* **低延迟**：内部延迟低于 3 毫秒，具有智能路由和负载均衡
* **企业级安全**：符合 SOC 2、HIPAA、GDPR 标准，具有 RBAC 和审计日志功能
* **配额和成本管理**：基于令牌的配额、速率限制和全面的使用跟踪
* **可观测性**：完整的请求/响应日志、指标和跟踪，具有可自定义的保留期

## TrueFoundry 如何与 CrewAI 集成

### 安装和设置

<Steps>
  <Step title="安装 CrewAI">
    ```bash  theme={null}
    pip install crewai
    ```
  </Step>

  <Step title="获取 TrueFoundry 访问令牌">
    1. 注册一个 [TrueFoundry 账户](https://www.truefoundry.com/register)
    2. 按照[快速入门](https://docs.truefoundry.com/gateway/quick-start)中的步骤操作
  </Step>

  <Step title="使用 TrueFoundry 配置 CrewAI">
        <img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/new-code-snippet.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=746c0bd23a77535f35b0b2bcf3320bf5" alt="TrueFoundry 代码配置" data-og-width="2940" width="2940" data-og-height="1664" height="1664" data-path="images/new-code-snippet.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/new-code-snippet.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=1d7f4e8883760766aa1ae1274fba2ffe 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/new-code-snippet.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=4604432c1e1121d24c3fa6ad93bc0bd9 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/new-code-snippet.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=8dd95282de37aa70090ac61a00b6e1bb 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/new-code-snippet.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=920a67bee38e979c770d775195b60864 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/new-code-snippet.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=4173b6e99ed12b00b54bf3f222589863 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/new-code-snippet.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=176dd84222c8c1a6f40af3e0adb88e37 2500w" />

    ```python  theme={null}
    from crewai import LLM

    # 使用 TrueFoundry AI 网关创建 LLM 实例
    truefoundry_llm = LLM(
        model="openai-main/gpt-4o",  # 同样，您可以调用任何提供商的任何模型
        base_url="your_truefoundry_gateway_base_url",
        api_key="your_truefoundry_api_key"
    )

    # 在您的 CrewAI 智能体中使用
    from crewai import Agent

    @agent
    def researcher(self) -> Agent:
        return Agent(
            config=self.agents_config['researcher'],
            llm=truefoundry_llm,
            verbose=True
        )
    ```
  </Step>
</Steps>

### 完整的 CrewAI 示例

```python  theme={null}
from crewai import Agent, Task, Crew, LLM

# 使用 TrueFoundry 配置 LLM
llm = LLM(
    model="openai-main/gpt-4o",
    base_url="your_truefoundry_gateway_base_url", 
    api_key="your_truefoundry_api_key"
)

# 创建智能体
researcher = Agent(
    role='研究分析师',
    goal='进行详细的市场研究',
    backstory='注重细节的专家市场分析师',
    llm=llm,
    verbose=True
)

writer = Agent(
    role='内容撰写人', 
    goal='创建全面的报告',
    backstory='经验丰富的技术撰稿人',
    llm=llm,
    verbose=True
)

# 创建任务
research_task = Task(
    description='研究 2024 年 AI 市场趋势',
    agent=researcher,
    expected_output='全面的研究摘要'
)

writing_task = Task(
    description='创建市场研究报告',
    agent=writer,
    expected_output='结构良好的报告并包含见解',
    context=[research_task]
)

# 创建并执行团队
crew = Crew(
    agents=[researcher, writer],
    tasks=[research_task, writing_task],
    verbose=True
)

result = crew.kickoff()
```

### 可观测性和治理

通过 TrueFoundry 的指标选项卡监控您的 CrewAI 智能体：
<img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/gateway-metrics.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=33755ff848cb457e162e806c20c98216" alt="TrueFoundry 指标" data-og-width="3840" width="3840" data-og-height="1984" height="1984" data-path="images/gateway-metrics.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/gateway-metrics.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=49a01b5e5bcc0429efd529860c020c10 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/gateway-metrics.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=3f47f171146339690e3516a892020626 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/gateway-metrics.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=857541d282cce3557f796ade097be01c 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/gateway-metrics.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=2f2b883b00e823ceb25ae1b747c656a4 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/gateway-metrics.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=9ddee789557bdbaacec42fd405180458 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/gateway-metrics.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=e9097f84b482e5c4da153d2d0271e6bf 2500w" />

使用 TrueFoundry 的 AI 网关，您可以监控和分析：

* **性能指标**：跟踪关键延迟指标，如请求延迟、首令牌时间（TTFS）和令牌间延迟（ITL），并提供 P99、P90 和 P50 百分位数据
* **成本和令牌使用情况**：通过详细的输入/输出令牌分解和每个模型的相关费用，了解应用程序的成本
* **使用模式**：通过用户活动、模型分布和基于团队的使用情况的详细分析，了解您的应用程序的使用方式
* **速率限制和负载均衡**：您可以为模型设置速率限制、负载均衡和故障转移

## 跟踪

有关跟踪的更多详细信息，请参阅 [跟踪入门指南](https://docs.truefoundry.com/docs/tracing/tracing-getting-started)。对于跟踪功能，您可以添加 Traceloop SDK：
对于跟踪功能，您可以添加 Traceloop SDK：

```bash  theme={null}
pip install traceloop-sdk
```

```python  theme={null}
from traceloop.sdk import Traceloop

# 初始化增强跟踪
Traceloop.init(
    api_endpoint="https://your-truefoundry-endpoint/api/tracing",
    headers={
        "Authorization": f"Bearer {your_truefoundry_pat_token}",
        "TFY-Tracing-Project": "your_project_name",
    },
)
```

这可以在整个 CrewAI 工作流程中提供额外的跟踪关联。
<img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/tracing_crewai.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=90623834e0ba9f4ccb09890f6824912d" alt="TrueFoundry CrewAI 跟踪" data-og-width="3024" width="3024" data-og-height="1720" height="1720" data-path="images/tracing_crewai.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/tracing_crewai.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=d05099079060dfd1588ac0c8de28e07b 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/tracing_crewai.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=645362e069e687f7dc6fd6c44a97a4ef 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/tracing_crewai.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=aac6d42bbd2f457b59f6a4b22d6a7be1 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/tracing_crewai.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=7f166e1329cef8da8c1e07a38dc75506 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/tracing_crewai.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=6e91cffda555b8cc7ce1800ed1b508b1 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/tracing_crewai.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=bf6296110bd62d9bb30ae2d0822d4b8d 2500w" />