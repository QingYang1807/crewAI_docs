# Portkey 集成

> 如何在 CrewAI 中使用 Portkey

<img src="https://raw.githubusercontent.com/siddharthsambharia-portkey/Portkey-Product-Images/main/Portkey-CrewAI.png" alt="Portkey CrewAI 标题图片" width="70%" />

## 简介

Portkey 通过生产就绪功能增强 CrewAI，将您的实验性代理团队转变为强大的系统，提供：

* **完全可观测性**，覆盖每个代理步骤、工具使用和交互
* **内置可靠性**，支持回退、重试和负载均衡
* **成本跟踪和优化**，管理您的 AI 支出
* **通过单一集成访问 200+ 大语言模型**
* **护栏**，确保代理行为安全和合规
* **版本控制提示**，保证代理性能一致

### 安装与设置

<Steps>
  <Step title="安装所需软件包">
    ```bash  theme={null}
    pip install -U crewai portkey-ai
    ```
  </Step>

  <Step title="生成 API 密钥" icon="lock">
    从 [Portkey 仪表板](https://app.portkey.ai/) 创建带有可选预算/速率限制的 Portkey API 密钥。您还可以为此密钥附加可靠性、缓存等配置。稍后会详细介绍。
  </Step>

  <Step title="用 Portkey 配置 CrewAI">
    集成很简单 - 您只需更新 CrewAI 设置中的 LLM 配置：

    ```python  theme={null}
    from crewai import LLM
    from portkey_ai import createHeaders, PORTKEY_GATEWAY_URL

    # 创建具有 Portkey 集成的 LLM 实例
    gpt_llm = LLM(
        model="gpt-4o",
        base_url=PORTKEY_GATEWAY_URL,
        api_key="dummy",  # 我们使用虚拟密钥，所以这是占位符
        extra_headers=createHeaders(
            api_key="YOUR_PORTKEY_API_KEY",
            virtual_key="YOUR_LLM_VIRTUAL_KEY",
            trace_id="unique-trace-id",               # 可选，用于请求跟踪
        )
    )

    # 在您的 Crew 代理中像这样使用它们：

    	@agent
    	def lead_market_analyst(self) -> Agent:
    		return Agent(
    			config=self.agents_config['lead_market_analyst'],
    			verbose=True,
    			memory=False,
    			llm=gpt_llm
    		)

    ```

    <Info>
      **什么是虚拟密钥？** Portkey 中的虚拟密钥将您的 LLM 提供商 API 密钥（OpenAI、Anthropic 等）安全地存储在加密保险库中。它们使密钥轮换和预算管理更加容易。[在此处了解更多关于虚拟密钥的信息](https://portkey.ai/docs/product/ai-gateway/virtual-keys)。
    </Info>
  </Step>
</Steps>

## 生产功能

### 1. 增强的可观测性

Portkey 为您的 CrewAI 代理提供全面的可观测性，帮助您准确了解每次执行期间发生的情况。

<Tabs>
  <Tab title="跟踪">
    <Frame>
      <img src="https://raw.githubusercontent.com/siddharthsambharia-portkey/Portkey-Product-Images/refs/heads/main/CrewAI%20Product%2011.1.webp" />
    </Frame>

    跟踪提供团队执行的层次视图，显示 LLM 调用、工具调用和状态转换的序列。

    ```python  theme={null}
    # 添加 trace_id 以在 Portkey 中启用层次跟踪
    portkey_llm = LLM(
        model="gpt-4o",
        base_url=PORTKEY_GATEWAY_URL,
        api_key="dummy",
        extra_headers=createHeaders(
            api_key="YOUR_PORTKEY_API_KEY",
            virtual_key="YOUR_OPENAI_VIRTUAL_KEY",
            trace_id="unique-session-id"  # 添加唯一跟踪 ID
        )
    )
    ```
  </Tab>

  <Tab title="日志">
    <Frame>
      <img src="https://raw.githubusercontent.com/siddharthsambharia-portkey/Portkey-Product-Images/refs/heads/main/CrewAI%20Portkey%20Docs%20Metadata.png" />
    </Frame>

    Portkey 记录与 LLM 的每次交互，包括：

    * 完整的请求和响应负载
    * 延迟和令牌使用指标
    * 成本计算
    * 工具调用和函数执行

    所有日志都可以按元数据、跟踪 ID、模型等过滤，使调试特定的团队运行变得容易。
  </Tab>

  <Tab title="指标和仪表板">
    <Frame>
      <img src="https://raw.githubusercontent.com/siddharthsambharia-portkey/Portkey-Product-Images/refs/heads/main/CrewAI%20Dashboard.png" />
    </Frame>

    Portkey 提供内置仪表板，帮助您：

    * 跟踪所有团队运行的成本和令牌使用
    * 分析延迟和成功率等性能指标
    * 识别代理工作流程中的瓶颈
    * 比较不同的团队配置和 LLM

    您可以按自定义元数据过滤和细分所有指标，以分析特定的团队类型、用户组或用例。
  </Tab>

  <Tab title="元数据过滤">
    <Frame>
      <img src="https://raw.githubusercontent.com/siddharthsambharia-portkey/Portkey-Product-Images/refs/heads/main/Metadata%20Filters%20from%20CrewAI.png" alt="使用元数据过滤器的分析" />
    </Frame>

    将自定义元数据添加到您的 CrewAI LLM 配置中，以实现强大的过滤和细分：

    ```python  theme={null}
    portkey_llm = LLM(
        model="gpt-4o",
        base_url=PORTKEY_GATEWAY_URL,
        api_key="dummy",
        extra_headers=createHeaders(
            api_key="YOUR_PORTKEY_API_KEY",
            virtual_key="YOUR_OPENAI_VIRTUAL_KEY",
            metadata={
                "crew_type": "research_crew",
                "environment": "production",
                "_user": "user_123",   # 用于用户分析的特殊 _user 字段
                "request_source": "mobile_app"
            }
        )
    )
    ```

    此元数据可用于在 Portkey 仪表板上过滤日志、跟踪和指标，允许您分析特定的团队运行、用户或环境。
  </Tab>
</Tabs>

### 2. 可靠性 - 保持团队平稳运行

在生产环境中运行团队时，可能会出现问题 - API 速率限制、网络问题或提供商服务中断。Portkey 的可靠性功能确保即使在出现问题时代代理也能平稳运行。

通过使用 Portkey 配置，可以在 CrewAI 设置中轻松启用回退：

```python  theme={null}
from crewai import LLM
from portkey_ai import createHeaders, PORTKEY_GATEWAY_URL

# 创建具有回退配置的 LLM
portkey_llm = LLM(
    model="gpt-4o",
    max_tokens=1000,
    base_url=PORTKEY_GATEWAY_URL,
    api_key="dummy",
    extra_headers=createHeaders(
        api_key="YOUR_PORTKEY_API_KEY",
        config={
            "strategy": {
                "mode": "fallback"
            },
            "targets": [
                {
                    "provider": "openai",
                    "api_key": "YOUR_OPENAI_API_KEY",
                    "override_params": {"model": "gpt-4o"}
                },
                {
                    "provider": "anthropic",
                    "api_key": "YOUR_ANTHROPIC_API_KEY",
                    "override_params": {"model": "claude-3-opus-20240229"}
                }
            ]
        }
    )
)

# 将此 LLM 配置与您的代理一起使用
```

如果 GPT-4o 请求失败，此配置将自动尝试 Claude，确保您的团队可以继续运行。

<CardGroup cols="2">
  <Card title="自动重试" icon="rotate" href="https://portkey.ai/docs/product/ai-gateway/automatic-retries">
    自动处理临时故障。如果 LLM 调用失败，Portkey 将重试相同的请求指定的次数 - 非常适合速率限制或网络故障。
  </Card>

  <Card title="请求超时" icon="clock" href="https://portkey.ai/docs/product/ai-gateway/request-timeouts">
    防止您的代理挂起。设置超时以确保您在所需时间内获得响应（或能够优雅地失败）。
  </Card>

  <Card title="条件路由" icon="route" href="https://portkey.ai/docs/product/ai-gateway/conditional-routing">
    将不同的请求发送到不同的提供商。根据您的需求，将复杂的推理发送到 GPT-4，创意任务发送到 Claude，快速响应发送到 Gemini。
  </Card>

  <Card title="回退" icon="shield" href="https://portkey.ai/docs/product/ai-gateway/fallbacks">
    即使主提供商失败也能保持运行。自动切换到备份提供商以保持可用性。
  </Card>

  <Card title="负载均衡" icon="scale-balanced" href="https://portkey.ai/docs/product/ai-gateway/load-balancing">
    跨多个 API 密钥或提供商分配请求。非常适合高容量团队操作并保持在速率限制内。
  </Card>
</CardGroup>

### 3. CrewAI 中的提示工程

Portkey 的提示工程工作室帮助您创建、管理和优化 CrewAI 代理中使用的提示。不要硬编码提示或指令，而是使用 Portkey 的提示渲染 API 动态获取和应用您的版本控制提示。

<Frame caption="在 Portkey 的提示库中管理提示">
  ![提示游乐场界面](https://raw.githubusercontent.com/siddharthsambharia-portkey/Portkey-Product-Images/refs/heads/main/CrewAI%20Portkey%20Docs.webp)
</Frame>

<Tabs>
  <Tab title="提示游乐场">
    提示游乐场是比较、测试和部署 AI 应用程序完美提示的地方。在这里，您可以尝试不同的模型，测试变量，比较输出，并在部署到生产环境之前完善提示工程策略。它允许您：

    1. 在代理中使用提示之前迭代开发
    2. 使用不同的变量和模型测试提示
    3. 比较不同提示版本之间的输出
    4. 与团队成员合作进行提示开发

    这种可视化环境使您更容易为 CrewAI 代理工作流程中的每个步骤制作有效的提示。
  </Tab>

  <Tab title="使用提示模板">
    提示渲染 API 检索您的提示模板并配置所有参数：

    ```python  theme={null}
    from crewai import Agent, LLM
    from portkey_ai import createHeaders, PORTKEY_GATEWAY_URL, Portkey

    # 初始化 Portkey 管理客户端
    portkey_admin = Portkey(api_key="YOUR_PORTKEY_API_KEY")

    # 使用渲染 API 检索提示
    prompt_data = portkey_client.prompts.render(
        prompt_id="YOUR_PROMPT_ID",
        variables={
            "agent_role": "高级研究科学家",
        }
    )

    backstory_agent_prompt=prompt_data.data.messages[0]["content"]


    # 使用 Portkey 集成设置 LLM
    portkey_llm = LLM(
        model="gpt-4o",
        base_url=PORTKEY_GATEWAY_URL,
        api_key="dummy",
        extra_headers=createHeaders(
            api_key="YOUR_PORTKEY_API_KEY",
            virtual_key="YOUR_OPENAI_VIRTUAL_KEY"
        )
    )

    # 使用渲染的提示创建代理
    researcher = Agent(
        role="高级研究科学家",
        goal="发现关于指定主题的开创性见解",
        backstory=backstory_agent,  # 使用渲染的提示
        verbose=True,
        llm=portkey_llm
    )
    ```
  </Tab>

  <Tab title="提示版本控制">
    您可以：

    * 创建同一提示的多个版本
    * 比较版本之间的性能
    * 需要时回滚到以前的版本
    * 在代码中指定要使用的版本：

    ```python  theme={null}
    # 使用特定的提示版本
    prompt_data = portkey_admin.prompts.render(
        prompt_id="YOUR_PROMPT_ID@version_number",
        variables={
            "agent_role": "高级研究科学家",
            "agent_goal": "发现开创性见解"
        }
    )
    ```
  </Tab>

  <Tab title="变量的 Mustache 模板">
    Portkey 提示使用 Mustache 风格的模板进行简单的变量替换：

    ```
    你是具有{{domain}}专业知识的{{agent_role}}。

    你的使命是通过利用你在该领域的知识和经验来{{agent_goal}}。

    始终保持{{tone}}语调，专注于提供{{focus_area}}。
    ```

    渲染时，只需传递变量：

    ```python  theme={null}
    prompt_data = portkey_admin.prompts.render(
        prompt_id="YOUR_PROMPT_ID",
        variables={
            "agent_role": "高级研究科学家",
            "domain": "人工智能",
            "agent_goal": "发现开创性见解",
            "tone": "专业",
            "focus_area": "实际应用"
        }
    )
    ```
  </Tab>
</Tabs>

<Card title="提示工程工作室" icon="wand-magic-sparkles" href="https://portkey.ai/docs/product/prompt-library">
  了解更多关于 Portkey 的提示管理功能
</Card>

### 4. 安全团队的护栏

护栏确保您的 CrewAI 代理在所有情况下都能安全运行并做出适当响应。

**为什么使用护栏？**

CrewAI 代理可能遇到各种故障模式：

* 生成有害或不适当的内容
* 泄露敏感信息，如 PII
* 幻觉错误信息
* 生成格式错误的输出

Portkey 的护栏为输入和输出添加了保护。

**实施护栏**

```python  theme={null}
from crewai import Agent, LLM
from portkey_ai import createHeaders, PORTKEY_GATEWAY_URL

# 创建具有护栏的 LLM
portkey_llm = LLM(
    model="gpt-4o",
    base_url=PORTKEY_GATEWAY_URL,
    api_key="dummy",
    extra_headers=createHeaders(
        api_key="YOUR_PORTKEY_API_KEY",
        virtual_key="YOUR_OPENAI_VIRTUAL_KEY",
        config={
            "input_guardrails": ["guardrails-id-xxx", "guardrails-id-yyy"],
            "output_guardrails": ["guardrails-id-zzz"]
        }
    )
)

# 创建具有护栏保护的 LLM 的代理
researcher = Agent(
    role="高级研究科学家",
    goal="发现关于指定主题的开创性见解",
    backstory="您是具有深厚领域知识的专家研究员。",
    verbose=True,
    llm=portkey_llm
)
```

Portkey 的护栏可以：

* 检测和编辑输入和输出中的 PII
* 过滤有害或不适当的内容
* 根据模式验证响应格式
* 根据事实检查幻觉
* 应用自定义业务逻辑和规则

<Card title="了解更多关于护栏" icon="shield-check" href="https://portkey.ai/docs/product/guardrails">
  探索 Portkey 的护栏功能以增强代理安全性
</Card>

### 5. 使用元数据跟踪用户

使用 Portkey 的元数据系统通过您的 CrewAI 代理跟踪单个用户。

**Portkey 中的元数据是什么？**

元数据允许您将自定义数据与每个请求关联，实现过滤、细分和分析。特殊的 `_user` 字段专为用户跟踪设计。

```python  theme={null}
from crewai import Agent, LLM
from portkey_ai import createHeaders, PORTKEY_GATEWAY_URL

# 使用用户跟踪配置 LLM
portkey_llm = LLM(
    model="gpt-4o",
    base_url=PORTKEY_GATEWAY_URL,
    api_key="dummy",
    extra_headers=createHeaders(
        api_key="YOUR_PORTKEY_API_KEY",
        virtual_key="YOUR_OPENAI_VIRTUAL_KEY",
        metadata={
            "_user": "user_123",  # 用于用户分析的特殊 _user 字段
            "user_tier": "premium",
            "user_company": "Acme Corp",
            "session_id": "abc-123"
        }
    )
)

# 创建具有跟踪 LLM 的代理
researcher = Agent(
    role="高级研究科学家",
    goal="发现关于指定主题的开创性见解",
    backstory="您是具有深厚领域知识的专家研究员。",
    verbose=True,
    llm=portkey_llm
)
```

**按用户过滤分析**

有了元数据，您可以按用户过滤分析并按用户分析性能指标：

<Frame caption="按用户过滤分析">
  <img src="https://raw.githubusercontent.com/siddharthsambharia-portkey/Portkey-Product-Images/refs/heads/main/Metadata%20Filters%20from%20CrewAI.png" />
</Frame>

这可以实现：

* 按用户成本跟踪和预算
* 个性化用户分析
* 团队或组织级别指标
* 环境特定监控（测试与生产）

<Card title="了解更多关于元数据" icon="tags" href="https://portkey.ai/docs/product/observability/metadata">
  探索如何使用自定义元数据来增强您的分析
</Card>

### 6. 高效团队的缓存

实施缓存以使您的 CrewAI 代理更高效和经济：

<Tabs>
  <Tab title="简单缓存">
    ```python  theme={null}
    from crewai import Agent, LLM
    from portkey_ai import createHeaders, PORTKEY_GATEWAY_URL

    # 使用简单缓存配置 LLM
    portkey_llm = LLM(
        model="gpt-4o",
        base_url=PORTKEY_GATEWAY_URL,
        api_key="dummy",
        extra_headers=createHeaders(
            api_key="YOUR_PORTKEY_API_KEY",
            virtual_key="YOUR_OPENAI_VIRTUAL_KEY",
            config={
                "cache": {
                    "mode": "simple"
                }
            }
        )
    )

    # 创建具有缓存的 LLM 的代理
    researcher = Agent(
        role="高级研究科学家",
        goal="发现关于指定主题的开创性见解",
        backstory="您是具有深厚领域知识的专家研究员。",
        verbose=True,
        llm=portkey_llm
    )
    ```

    简单缓存对输入提示执行精确匹配，缓存相同的请求以避免冗余的模型执行。
  </Tab>

  <Tab title="语义缓存">
    ```python  theme={null}
    from crewai import Agent, LLM
    from portkey_ai import createHeaders, PORTKEY_GATEWAY_URL

    # 使用语义缓存配置 LLM
    portkey_llm = LLM(
        model="gpt-4o",
        base_url=PORTKEY_GATEWAY_URL,
        api_key="dummy",
        extra_headers=createHeaders(
            api_key="YOUR_PORTKEY_API_KEY",
            virtual_key="YOUR_OPENAI_VIRTUAL_KEY",
            config={
                "cache": {
                    "mode": "semantic"
                }
            }
        )
    )

    # 创建具有语义缓存的 LLM 的代理
    researcher = Agent(
        role="高级研究科学家",
        goal="发现关于指定主题的开创性见解",
        backstory="您是具有深厚领域知识的专家研究员。",
        verbose=True,
        llm=portkey_llm
    )
    ```

    语义缓存考虑输入请求之间的上下文相似性，缓存语义相似输入的响应。
  </Tab>
</Tabs>

### 7. 模型互操作性

CrewAI 支持多个 LLM 提供商，Portkey 通过统一界面提供对 200 多个 LLM 的访问来扩展此功能。您可以轻松切换不同的模型，而无需更改核心代理逻辑：

```python  theme={null}
from crewai import Agent, LLM
from portkey_ai import createHeaders, PORTKEY_GATEWAY_URL

# 设置不同提供商的 LLM
openai_llm = LLM(
    model="gpt-4o",
    base_url=PORTKEY_GATEWAY_URL,
    api_key="dummy",
    extra_headers=createHeaders(
        api_key="YOUR_PORTKEY_API_KEY",
        virtual_key="YOUR_OPENAI_VIRTUAL_KEY"
    )
)

anthropic_llm = LLM(
    model="claude-3-5-sonnet-latest",
    max_tokens=1000,
    base_url=PORTKEY_GATEWAY_URL,
    api_key="dummy",
    extra_headers=createHeaders(
        api_key="YOUR_PORTKEY_API_KEY",
        virtual_key="YOUR_ANTHROPIC_VIRTUAL_KEY"
    )
)

# 根据您的需求选择要用于每个代理的 LLM
researcher = Agent(
    role="高级研究科学家",
    goal="发现关于指定主题的开创性见解",
    backstory="您是具有深厚领域知识的专家研究员。",
    verbose=True,
    llm=openai_llm  # 对于 Anthropic 使用 anthropic_llm
)
```

Portkey 提供来自以下提供商的 LLM 访问：

* OpenAI (GPT-4o, GPT-4 Turbo 等)
* Anthropic (Claude 3.5 Sonnet, Claude 3 Opus 等)
* Mistral AI (Mistral Large, Mistral Medium 等)
* Google Vertex AI (Gemini 1.5 Pro 等)
* Cohere (Command, Command-R 等)
* AWS Bedrock (Claude, Titan 等)
* 本地/私有模型

<Card title="支持的提供商" icon="server" href="https://portkey.ai/docs/integrations/llms">
  查看 Portkey 支持的完整 LLM 提供商列表
</Card>

## 为 CrewAI 设置企业治理

**为什么需要企业治理？**
如果您在组织内部使用 CrewAI，您需要考虑几个治理方面：

* **成本管理**：跨团队控制和跟踪 AI 支出
* **访问控制**：管理哪些团队可以使用特定模型
* **使用分析**：了解 AI 在整个组织中的使用方式
* **安全与合规**：维护企业安全标准
* **可靠性**：确保所有用户的服务一致

Portkey 添加了全面的治理层来满足这些企业需求。让我们逐步实施这些控制。

<Steps>
  <Step title="创建虚拟密钥">
    虚拟密钥是 Portkey 安全管理 LLM 提供商 API 密钥的方式。它们提供基本控制，如：

    * API 使用的预算限制
    * 速率限制功能
    * 安全的 API 密钥存储

    要创建虚拟密钥：
    转到 Portkey 应用程序中的[虚拟密钥](https://app.portkey.ai/virtual-keys)。保存并复制虚拟密钥 ID

    <Frame>
      <img src="https://raw.githubusercontent.com/siddharthsambharia-portkey/Portkey-Product-Images/refs/heads/main/Virtual%20Key%20from%20Portkey%20Docs.png" width="500" />
    </Frame>

    <Note>
      保存您的虚拟密钥 ID - 下一步将需要它。
    </Note>
  </Step>

  <Step title="创建默认配置">
    Portkey 中的配置定义了您的请求如何路由，具有高级路由、回退和重试等功能。

    要创建您的配置：

    1. 转到 Portkey 仪表板中的[配置](https://app.portkey.ai/configs)
    2. 使用以下内容创建新配置：
       ```json  theme={null}
       {
           "virtual_key": "YOUR_VIRTUAL_KEY_FROM_STEP1",
          	"override_params": {
             "model": "gpt-4o" // 您的首选模型名称
           }
       }
       ```
    3. 保存并记下配置名称以供下一步使用

    <Frame>
      <img src="https://raw.githubusercontent.com/siddharthsambharia-portkey/Portkey-Product-Images/refs/heads/main/CrewAI%20Portkey%20Docs%20Config.png" width="500" />
    </Frame>
  </Step>

  <Step title="配置 Portkey API 密钥">
    现在创建 Portkey API 密钥并附加您在步骤 2 中创建的配置：

    1. 转到 Portkey 中的[API 密钥](https://app.portkey.ai/api-keys)并创建新 API 密钥
    2. 从`步骤 2`中选择您的配置
    3. 生成并保存您的 API 密钥

    <Frame>
      <img src="https://raw.githubusercontent.com/siddharthsambharia-portkey/Portkey-Product-Images/refs/heads/main/CrewAI%20API%20Key.png" width="500" />
    </Frame>
  </Step>

  <Step title="连接到 CrewAI">
    设置带有附加配置的 Portkey API 密钥后，将其连接到您的 CrewAI 代理：

    ```python  theme={null}
    from crewai import Agent, LLM
    from portkey_ai import PORTKEY_GATEWAY_URL

    # 使用您的 API 密钥配置 LLM
    portkey_llm = LLM(
        model="gpt-4o",
        base_url=PORTKEY_GATEWAY_URL,
        api_key="YOUR_PORTKEY_API_KEY"
    )

    # 创建具有启用 Portkey 的 LLM 的代理
    researcher = Agent(
        role="高级研究科学家",
        goal="发现关于指定主题的开创性见解",
        backstory="您是具有深厚领域知识的专家研究员。",
        verbose=True,
        llm=portkey_llm
    )
    ```
  </Step>
</Steps>

<AccordionGroup>
  <Accordion title="步骤 1：实施预算控制和速率限制">
    ### 步骤 1：实施预算控制和速率限制

    虚拟密钥能够在团队/部门级别对 LLM 访问进行细粒度控制。这有助于您：

    * 设置[预算限制](https://portkey.ai/docs/product/ai-gateway/virtual-keys/budget-limits)
    * 使用速率限制防止意外使用峰值
    * 跟踪部门支出

    #### 设置特定于部门的控制：

    1. 在 Portkey 仪表板中导航到[虚拟密钥](https://app.portkey.ai/virtual-keys)
    2. 为每个部门创建具有预算限制和速率限制的新虚拟密钥
    3. 配置特定于部门的限制

    <Frame>
      <img src="https://raw.githubusercontent.com/siddharthsambharia-portkey/Portkey-Product-Images/refs/heads/main/Virtual%20Key%20from%20Portkey%20Docs.png" width="500" />
    </Frame>
  </Accordion>

  <Accordion title="步骤 2：定义模型访问规则">
    ### 步骤 2：定义模型访问规则

    随着 AI 使用的扩展，控制哪些团队可以访问特定模型变得至关重要。Portkey 配置提供此控制层，具有以下功能：

    #### 访问控制功能：

    * **模型限制**：限制对特定模型的访问
    * **数据保护**：为敏感数据实施护栏
    * **可靠性控制**：添加回退和重试逻辑

    #### 示例配置：

    这是将请求路由到 OpenAI（特别是使用 GPT-4o）的基本配置：

    ```json  theme={null}
    {
    	"strategy": {
    		"mode": "single"
    	},
    	"targets": [
    		{
    			"virtual_key": "YOUR_OPENAI_VIRTUAL_KEY",
    			"override_params": {
    				"model": "gpt-4o"
    			}
    		}
    	]
    }
    ```

    在您的 Portkey 仪表板的[配置页面](https://app.portkey.ai/configs)上创建您的配置。

    <Note>
      配置可以随时更新以调整控制，而不影响运行中的应用程序。
    </Note>
  </Accordion>

  <Accordion title="步骤 3：实施访问控制">
    ### 步骤 3：实施访问控制

    创建用户特定的 API 密钥，自动：

    * 借助虚拟密钥按用户/团队跟踪使用情况
    * 应用适当的配置来路由请求
    * 收集相关元数据以过滤日志
    * 强制执行访问权限

    通过以下方式创建 API 密钥：

    * [Portkey 应用程序](https://app.portkey.ai/)
    * [API 密钥管理 API](/en/api-reference/admin-api/control-plane/api-keys/create-api-key)

    使用 Python SDK 的示例：

    ```python  theme={null}
    from portkey_ai import Portkey

    portkey = Portkey(api_key="YOUR_ADMIN_API_KEY")

    api_key = portkey.api_keys.create(
        name="engineering-team",
        type="organisation",
        workspace_id="YOUR_WORKSPACE_ID",
        defaults={
            "config_id": "your-config-id",
            "metadata": {
                "environment": "production",
                "department": "engineering"
            }
        },
        scopes=["logs.view", "configs.read"]
    )
    ```

    有关详细的密钥管理说明，请参阅我们的 [API 密钥文档](/en/api-reference/admin-api/control-plane/api-keys/create-api-key)。
  </Accordion>

  <Accordion title="步骤 4：部署和监控">
    ### 步骤 4：部署和监控

    将 API 密钥分发给团队成员后，您企业就绪的 CrewAI 设置已准备就绪。现在每个团队成员都可以使用其指定的 API 密钥，具有适当的访问级别和预算控制。

    在 Portkey 仪表板中监控使用情况：

    * 按部门的成本跟踪
    * 模型使用模式
    * 请求量
    * 错误率
  </Accordion>
</AccordionGroup>

<Note>
  ### 企业功能现已可用

  **您的 CrewAI 集成现在具有：**

  * 部门预算控制
  * 模型访问治理
  * 使用跟踪和归属
  * 安全护栏
  * 可靠性功能
</Note>

## 常见问题

<AccordionGroup>
  <Accordion title="Portkey 如何增强 CrewAI？">
    Portkey 通过全面的可观测性（跟踪、日志、指标）、可靠性功能（回退、重试、缓存）以及通过统一界面访问 200+ LLM，为 CrewAI 添加生产就绪性。这使得调试、优化和扩展代理应用程序变得更加容易。
  </Accordion>

  <Accordion title="我可以将 Portkey 与现有的 CrewAI 应用程序一起使用吗？">
    是的！Portkey 与现有的 CrewAI 应用程序无缝集成。您只需使用启用 Portkey 的版本更新 LLM 配置代码。您的代理和团队代码的其余部分保持不变。
  </Accordion>

  <Accordion title="Portkey 是否适用于所有 CrewAI 功能？">
    Portkey 支持所有 CrewAI 功能，包括代理、工具、人机循环工作流程以及所有任务过程类型（顺序、层次等）。它在任何功能限制的情况下添加可观测性和可靠性。
  </Accordion>

  <Accordion title="我可以在团队中的多个代理中跟踪使用情况吗？">
    是的，Portkey 允许您在团队中的多个代理中使用一致的 `trace_id` 来跟踪整个工作流程。这对于复杂的团队特别有用，在复杂的团队中您希望了解跨多个代理的完整执行路径。
  </Accordion>

  <Accordion title="如何过滤特定团队运行的日志和跟踪？">
    Portkey 允许您将自定义元数据添加到 LLM 配置中，然后您可以用于过滤。添加如 `crew_name`、`crew_type` 或 `session_id` 等字段，以轻松查找和分析特定的团队执行。
  </Accordion>

  <Accordion title="我可以将自己的 API 密钥与 Portkey 一起使用吗？">
    是的！Portkey 使用您自己的各种 LLM 提供商的 API 密钥。它将它们安全地存储为虚拟密钥，使您可以轻松管理和轮换密钥，而无需更改代码。
  </Accordion>
</AccordionGroup>

## 资源

<CardGroup cols="3">
  <Card title="CrewAI 文档" icon="book" href="https://docs.crewai.com/">
    <p>官方 CrewAI 文档</p>
  </Card>

  <Card title="预约演示" icon="calendar" href="https://calendly.com/portkey-ai">
    <p>获取实施此集成的个性化指导</p>
  </Card>
</CardGroup>