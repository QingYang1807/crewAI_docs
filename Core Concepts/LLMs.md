# 大型语言模型（LLMs）

> 在CrewAI项目中配置和使用大型语言模型（LLMs）的综合指南

## 概述

CrewAI通过提供商的原生SDK与多个LLM提供商集成，让您能够为特定用例选择合适的模型。本指南将帮助您了解如何在CrewAI项目中配置和使用不同的LLM提供商。

## 什么是LLMs？

大型语言模型（LLMs）是CrewAI智能体背后的核心智能。它们使智能体能够理解上下文、做出决策并生成类人响应。以下是您需要了解的内容：

<CardGroup cols={2}>
  <Card title="LLM基础知识" icon="brain">
    大型语言模型是在海量文本数据上训练的AI系统。它们为您的CrewAI智能体提供智能，使其能够理解和生成类人文本。
  </Card>

  <Card title="上下文窗口" icon="window">
    上下文窗口决定了一个LLM一次可以处理多少文本。更大的窗口（例如128K令牌）允许更多上下文，但可能更昂贵且更慢。
  </Card>

  <Card title="温度" icon="temperature-three-quarters">
    温度（0.0到1.0）控制响应的随机性。较低的值（例如0.2）产生更专注、确定性的输出，而较高的值（例如0.8）增加创造力和可变性。
  </Card>

  <Card title="提供商选择" icon="server">
    每个LLM提供商（例如OpenAI、Anthropic、Google）提供具有不同能力、定价和功能的模型。根据您对准确性、速度和成本的需求进行选择。
  </Card>
</CardGroup>

## 设置您的LLM

在CrewAI代码中有不同地方可以指定要使用的模型。一旦指定了您使用的模型，您需要为每个模型提供商提供配置（如API密钥）。请参阅[提供商配置示例](#provider-configuration-examples)部分了解您的提供商。

<Tabs>
  <Tab title="1. 环境变量">
    最简单的入门方法。直接在环境、`.env`文件或应用程序代码中设置模型。如果您使用`crewai create`来引导项目，它已经设置好了。

    ```bash .env theme={null}
    MODEL=model-id  # 例如 gpt-4o, gemini-2.0-flash, claude-3-sonnet-...

    # 确保也在此处设置您的API密钥。请参阅下面的提供商部分。
    ```

    <Warning>
      永远不要将API密钥提交到版本控制。使用环境文件(.env)或您系统的秘密管理。
    </Warning>
  </Tab>

  <Tab title="2. YAML配置">
    创建一个YAML文件来定义您的智能体配置。这种方法非常适合版本控制和团队协作：

    ```yaml agents.yaml {6} theme={null}
    researcher:
        role: 研究专员
        goal: 进行全面研究和分析
        backstory: 一位拥有多年经验的专注研究专业人士
        verbose: true
        llm: provider/model-id  # 例如 openai/gpt-4o, google/gemini-2.0-flash, anthropic/claude...
        # （更多内容请参见下面的提供商配置示例）
    ```

    <Info>
      YAML配置允许您：

      * 版本控制您的智能体设置
      * 在不同模型之间轻松切换
      * 在团队成员之间共享配置
      * 记录模型选择及其目的
    </Info>
  </Tab>

  <Tab title="3. 直接代码">
    为获得最大灵活性，直接在Python代码中配置LLMs：

    ```python {4,8} theme={null}
    from crewai import LLM

    # 基本配置
    llm = LLM(model="model-id-here")  # gpt-4o, gemini-2.0-flash, anthropic/claude...

    # 带有详细参数的高级配置
    llm = LLM(
        model="model-id-here",  # gpt-4o, gemini-2.0-flash, anthropic/claude...
        temperature=0.7,        # 更高值以获得更创意的输出
        timeout=120,            # 等待响应的秒数
        max_tokens=4000,        # 响应的最大长度
        top_p=0.9,              # 核采样参数
        frequency_penalty=0.1 , # 减少重复
        presence_penalty=0.1,   # 鼓励主题多样性
        response_format={"type": "json"},  # 用于结构化输出
        seed=42                 # 用于可重现的结果
    )
    ```

    <Info>
      参数解释：

      * `temperature`：控制随机性（0.0-1.0）
      * `timeout`：响应的最大等待时间
      * `max_tokens`：限制响应长度
      * `top_p`：采样的温度替代方法
      * `frequency_penalty`：减少词语重复
      * `presence_penalty`：鼓励新主题
      * `response_format`：指定输出结构
      * `seed`：确保一致的输出
    </Info>
  </Tab>
</Tabs>

## 提供商配置示例

CrewAI支持众多LLM提供商，每个提供商提供独特的功能、身份验证方法和模型功能。
在本节中，您将找到详细的示例，帮助您选择、配置和优化最适合您项目需求的LLM。

<AccordionGroup>
  <Accordion title="OpenAI">
    CrewAI通过OpenAI Python SDK提供与OpenAI的原生集成。

    ```toml Code theme={null}
    # 必需
    OPENAI_API_KEY=sk-...

    # 可选
    OPENAI_BASE_URL=<custom-base-url>
    ```

    **基本用法：**

    ```python Code theme={null}
    from crewai import LLM

    llm = LLM(
        model="openai/gpt-4o",
        api_key="your-api-key",  # 或设置 OPENAI_API_KEY
        temperature=0.7,
        max_tokens=4000
    )
    ```

    **高级配置：**

    ```python Code theme={null}
    from crewai import LLM

    llm = LLM(
        model="openai/gpt-4o",
        api_key="your-api-key",
        base_url="https://api.openai.com/v1",  # 可选自定义端点
        organization="org-...",  # 可选组织ID
        project="proj_...",  # 可选项目ID
        temperature=0.7,
        max_tokens=4000,
        max_completion_tokens=4000,  # 用于较新模型
        top_p=0.9,
        frequency_penalty=0.1,
        presence_penalty=0.1,
        stop=["END"],
        seed=42,  # 用于可重现输出
        stream=True,  # 启用流式传输
        timeout=60.0,  # 请求超时（秒）
        max_retries=3,  # 最大重试尝试次数
        logprobs=True,  # 返回对数概率
        top_logprobs=5,  # 最可能令牌的数量
        reasoning_effort="medium"  # 对于o1模型：low, medium, high
    )
    ```

    **结构化输出：**

    ```python Code theme={null}
    from pydantic import BaseModel
    from crewai import LLM

    class ResponseFormat(BaseModel):
        name: str
        age: int
        summary: str

    llm = LLM(
        model="openai/gpt-4o",
    )
    ```

    **支持的环境变量：**

    * `OPENAI_API_KEY`：您的OpenAI API密钥（必需）
    * `OPENAI_BASE_URL`：OpenAI API的自定义基础URL（可选）

    **功能：**

    * 原生函数调用支持（o1模型除外）
    * 使用JSON模式的结构化输出
    * 实时响应的流式传输支持
    * 令牌使用跟踪
    * 停止序列支持（o1模型除外）
    * 令牌级见解的对数概率
    * o1模型的推理努力控制

    **支持的模型：**

    | 模型 | 上下文窗口 | 最适用于 |
    | ------------ | -------------- | ------------------------------------------- |
    | gpt-4.1 | 1M令牌 | 具有增强功能的最新模型 |
    | gpt-4.1-mini | 1M令牌 | 具有大上下文的高效版本 |
    | gpt-4.1-nano | 1M令牌 | 超高效变体 |
    | gpt-4o | 128,000令牌 | 针对速度和智能优化 |
    | gpt-4o-mini | 200,000令牌 | 具有大上下文的成本效益 |
    | gpt-4-turbo | 128,000令牌 | 长篇内容、文档分析 |
    | gpt-4 | 8,192令牌 | 高精度任务、复杂推理 |
    | o1 | 200,000令牌 | 高级推理、复杂问题解决 |
    | o1-preview | 128,000令牌 | 推理能力预览 |
    | o1-mini | 128,000令牌 | 高效推理模型 |
    | o3-mini | 200,000令牌 | 轻量级推理模型 |
    | o4-mini | 200,000令牌 | 下一代高效推理 |

    **注意：**要使用OpenAI，请安装所需的依赖项：

    ```bash  theme={null}
    uv add "crewai[openai]"
    ```
  </Accordion>

  <Accordion title="Meta-Llama">
    Meta的Llama API提供对Meta大型语言模型系列的访问。
    该API可通过[Meta Llama API](https://llama.developer.meta.com?utm_source=partner-crewai\&utm_medium=website)获得。
    在您的`.env`文件中设置以下环境变量：

    ```toml Code theme={null}
    # Meta Llama API密钥配置
    LLAMA_API_KEY=LLM|your_api_key_here
    ```

    在您的CrewAI项目中的使用示例：

    ```python Code theme={null}
    from crewai import LLM

    # 初始化Meta Llama LLM
    llm = LLM(
        model="meta_llama/Llama-4-Scout-17B-16E-Instruct-FP8",
        temperature=0.8,
        stop=["END"],
        seed=42
    )
    ```

    此处列出的所有模型[https://llama.developer.meta.com/docs/models/](https://llama.developer.meta.com/docs/models/)都受支持。

    | 模型ID | 输入上下文长度 | 输出上下文长度 | 输入模态 | 输出模态 |
    | --------------------------------------------------- | -------------------- | --------------------- | ---------------- | ----------------- |
    | `meta_llama/Llama-4-Scout-17B-16E-Instruct-FP8` | 128k | 4028 | 文本、图像 | 文本 |
    | `meta_llama/Llama-4-Maverick-17B-128E-Instruct-FP8` | 128k | 4028 | 文本、图像 | 文本 |
    | `meta_llama/Llama-3.3-70B-Instruct` | 128k | 4028 | 文本 | 文本 |
    | `meta_llama/Llama-3.3-8B-Instruct` | 128k | 4028 | 文本 | 文本 |
  </Accordion>

  <Accordion title="Anthropic">
    CrewAI通过Anthropic Python SDK提供与Anthropic的原生集成。

    ```toml Code theme={null}
    # 必需
    ANTHROPIC_API_KEY=sk-ant-...
    ```

    **基本用法：**

    ```python Code theme={null}
    from crewai import LLM

    llm = LLM(
        model="anthropic/claude-3-5-sonnet-20241022",
        api_key="your-api-key",  # 或设置 ANTHROPIC_API_KEY
        max_tokens=4096  # Anthropic必需
    )
    ```

    **高级配置：**

    ```python Code theme={null}
    from crewai import LLM

    llm = LLM(
        model="anthropic/claude-3-5-sonnet-20241022",
        api_key="your-api-key",
        base_url="https://api.anthropic.com",  # 可选自定义端点
        temperature=0.7,
        max_tokens=4096,  # 必需参数
        top_p=0.9,
        stop_sequences=["END", "STOP"],  # Anthropic使用stop_sequences
        stream=True,  # 启用流式传输
        timeout=60.0,  # 请求超时（秒）
        max_retries=3  # 最大重试尝试次数
    )
    ```

    **支持的环境变量：**

    * `ANTHROPIC_API_KEY`：您的Anthropic API密钥（必需）

    **功能：**

    * Claude 3+模型的原生工具使用支持
    * 实时响应的流式传输支持
    * 自动系统消息处理
    * 受控输出的停止序列
    * 令牌使用跟踪
    * 多轮工具使用对话

    **重要说明：**

    * `max_tokens`是所有Anthropic模型的**必需**参数
    * Claude使用`stop_sequences`而不是`stop`
    * 系统消息与对话消息分开处理
    * 第一条消息必须来自用户（自动处理）
    * 消息必须在用户和助手之间交替

    **支持的模型：**

    | 模型 | 上下文窗口 | 最适用于 |
    | -------------------------- | -------------- | ---------------------------------------- |
    | claude-3-7-sonnet | 200,000令牌 | 高级推理和智能体任务 |
    | claude-3-5-sonnet-20241022 | 200,000令牌 | 具有最佳性能的最新Sonnet |
    | claude-3-5-haiku | 200,000令牌 | 快速、紧凑模型，用于快速响应 |
    | claude-3-opus | 200,000令牌 | 最适合复杂任务 |
    | claude-3-sonnet | 200,000令牌 | 平衡智能和速度 |
    | claude-3-haiku | 200,000令牌 | 最快的简单任务处理 |
    | claude-2.1 | 200,000令牌 | 扩展上下文，减少幻觉 |
    | claude-2 | 100,000令牌 | 多功能模型，适用于各种任务 |
    | claude-instant | 100,000令牌 | 快速、成本效益的日常任务处理 |

    **注意：**要使用Anthropic，请安装所需的依赖项：

    ```bash  theme={null}
    uv add "crewai[anthropic]"
    ```
  </Accordion>

  <Accordion title="Google (Gemini API)">
    CrewAI通过Google Gen AI Python SDK提供与Google Gemini的原生集成。

    在您的`.env`文件中设置您的API密钥。如果您需要密钥，请查看[AI Studio](https://aistudio.google.com/apikey)。

    ```toml .env theme={null}
    # 必需（以下之一）
    GOOGLE_API_KEY=<your-api-key>
    GEMINI_API_KEY=<your-api-key>

    # 可选 - 用于Vertex AI
    GOOGLE_CLOUD_PROJECT=<your-project-id>
    GOOGLE_CLOUD_LOCATION=<location>  # 默认为us-central1
    GOOGLE_GENAI_USE_VERTEXAI=true  # 设置为使用Vertex AI
    ```

    **基本用法：**

    ```python Code theme={null}
    from crewai import LLM

    llm = LLM(
        model="gemini/gemini-2.0-flash",
        api_key="your-api-key",  # 或设置 GOOGLE_API_KEY/GEMINI_API_KEY
        temperature=0.7
    )
    ```

    **高级配置：**

    ```python Code theme={null}
    from crewai import LLM

    llm = LLM(
        model="gemini/gemini-2.5-flash",
        api_key="your-api-key",
        temperature=0.7,
        top_p=0.9,
        top_k=40,  # Top-k采样参数
        max_output_tokens=8192,
        stop_sequences=["END", "STOP"],
        stream=True,  # 启用流式传输
        safety_settings={
            "HARM_CATEGORY_HARASSMENT": "BLOCK_NONE",
            "HARM_CATEGORY_HATE_SPEECH": "BLOCK_NONE"
        }
    )
    ```

    **Vertex AI配置：**

    ```python Code theme={null}
    from crewai import LLM

    llm = LLM(
        model="gemini/gemini-1.5-pro",
        project="your-gcp-project-id",
        location="us-central1"  # GCP区域
    )
    ```

    **支持的环境变量：**

    * `GOOGLE_API_KEY`或`GEMINI_API_KEY`：您的Google API密钥（Gemini API必需）
    * `GOOGLE_CLOUD_PROJECT`：Google Cloud项目ID（用于Vertex AI）
    * `GOOGLE_CLOUD_LOCATION`：GCP位置（默认为`us-central1`）
    * `GOOGLE_GENAI_USE_VERTEXAI`：设置为`true`以使用Vertex AI

    **功能：**

    * Gemini 1.5+和2.x模型的原生函数调用支持
    * 实时响应的流式传输支持
    * 多模态功能（文本、图像、视频）
    * 安全设置配置
    * 支持Gemini API和Vertex AI
    * 自动系统指令处理
    * 令牌使用跟踪

    **Gemini模型：**

    Google提供一系列针对不同用例优化的强大模型。

    | 模型 | 上下文窗口 | 最适用于 |
    | ------------------------- | -------------- | --------------------------------------------------------- |
    | gemini-2.5-flash | 1M令牌 | 自适应思考，成本效益 |
    | gemini-2.5-pro | 1M令牌 | 增强思考和推理，多模态理解 |
    | gemini-2.0-flash | 1M令牌 | 下一代功能，速度，思考 |
    | gemini-2.0-flash-thinking | 32,768令牌 | 具有思考过程的高级推理 |
    | gemini-2.0-flash-lite | 1M令牌 | 成本效益和低延迟 |
    | gemini-1.5-pro | 2M令牌 | 最佳性能，逻辑推理，编码 |
    | gemini-1.5-flash | 1M令牌 | 平衡的多模态模型，适用于大多数任务 |
    | gemini-1.5-flash-8b | 1M令牌 | 最快，最具成本效益 |
    | gemini-1.0-pro | 32,768令牌 | 早期模型 |

    **Gemma模型：**

    Gemini API还支持托管在Google基础设施上的[Gemma模型](https://ai.google.dev/gemma/docs)。

    | 模型 | 上下文窗口 | 最适用于 |
    | ----------- | -------------- | ----------------------------------- |
    | gemma-3-1b | 32,000令牌 | 超轻量级任务 |
    | gemma-3-4b | 128,000令牌 | 高效通用任务 |
    | gemma-3-12b | 128,000令牌 | 平衡性能和效率 |
    | gemma-3-27b | 128,000令牌 | 高性能任务 |

    **注意：**要使用Google Gemini，请安装所需的依赖项：

    ```bash  theme={null}
    uv add "crewai[google-genai]"
    ```

    完整的模型列表可在[Gemini模型文档](https://ai.google.dev/gemini-api/docs/models)中找到。
  </Accordion>

  <Accordion title="Google (Vertex AI)">
    从您的Google Cloud Console获取凭据并将其保存到JSON文件，然后使用以下代码加载：

    ```python Code theme={null}
    import json

    file_path = 'path/to/vertex_ai_service_account.json'

    # 加载JSON文件
    with open(file_path, 'r') as file:
        vertex_credentials = json.load(file)

    # 将凭据转换为JSON字符串
    vertex_credentials_json = json.dumps(vertex_credentials)
    ```

    在您的CrewAI项目中的使用示例：

    ```python Code theme={null}
    from crewai import LLM

    llm = LLM(
        model="gemini-1.5-pro-latest", # 或 vertex_ai/gemini-1.5-pro-latest
        temperature=0.7,
        vertex_credentials=vertex_credentials_json
    )
    ```

    Google提供一系列针对不同用例优化的强大模型：

    | 模型 | 上下文窗口 | 最适用于 |
    | ------------------------------ | -------------- | ---------------------------------------------------------------------------------------------------------------- |
    | gemini-2.5-flash-preview-04-17 | 1M令牌 | 自适应思考，成本效益 |
    | gemini-2.5-pro-preview-05-06 | 1M令牌 | 增强思考和推理，多模态理解，高级编码等 |
    | gemini-2.0-flash | 1M令牌 | 下一代功能，速度，思考和实时流式传输 |
    | gemini-2.0-flash-lite | 1M令牌 | 成本效益和低延迟 |
    | gemini-1.5-flash | 1M令牌 | 平衡的多模态模型，适用于大多数任务 |
    | gemini-1.5-flash-8B | 1M令牌 | 最快，最具成本效益，适用于高频任务 |
    | gemini-1.5-pro | 2M令牌 | 最佳性能，各种推理任务，包括逻辑推理，编码和创意协作 |
  </Accordion>

  <Accordion title="Azure">
    CrewAI通过Azure AI Inference Python SDK提供与Azure AI Inference和Azure OpenAI的原生集成。

    ```toml Code theme={null}
    # 必需
    AZURE_API_KEY=<your-api-key>
    AZURE_ENDPOINT=<your-endpoint-url>

    # 可选
    AZURE_API_VERSION=<api-version>  # 默认为2024-06-01
    ```

    **端点URL格式：**

    对于Azure OpenAI部署：

    ```
    https://<resource-name>.openai.azure.com/openai/deployments/<deployment-name>
    ```

    对于Azure AI Inference端点：

    ```
    https://<resource-name>.inference.azure.com
    ```

    **基本用法：**

    ```python Code theme={null}
    llm = LLM(
        model="azure/gpt-4",
        api_key="<your-api-key>",  # 或设置 AZURE_API_KEY
        endpoint="<your-endpoint-url>",
        api_version="2024-06-01"
    )
    ```

    **高级配置：**

    ```python Code theme={null}
    llm = LLM(
        model="azure/gpt-4o",
        temperature=0.7,
        max_tokens=4000,
        top_p=0.9,
        frequency_penalty=0.0,
        presence_penalty=0.0,
        stop=["END"],
        stream=True,
        timeout=60.0,
        max_retries=3
    )
    ```

    **支持的环境变量：**

    * `AZURE_API_KEY`：您的Azure API密钥（必需）
    * `AZURE_ENDPOINT`：您的Azure端点URL（必需，也检查`AZURE_OPENAI_ENDPOINT`和`AZURE_API_BASE`）
    * `AZURE_API_VERSION`：API版本（可选，默认为`2024-06-01`）

    **功能：**

    * Azure OpenAI模型的原生函数调用支持（gpt-4、gpt-4o、gpt-3.5-turbo等）
    * 实时响应的流式传输支持
    * 自动端点URL验证和更正
    * 具有重试逻辑的全面错误处理
    * 令牌使用跟踪

    **注意：**要使用Azure AI Inference，请安装所需的依赖项：

    ```bash  theme={null}
    uv add "crewai[azure-ai-inference]"
    ```
  </Accordion>

  <Accordion title="AWS Bedrock">
    CrewAI通过boto3 SDK使用Converse API提供与AWS Bedrock的原生集成。

    ```toml Code theme={null}
    # 必需
    AWS_ACCESS_KEY_ID=<your-access-key>
    AWS_SECRET_ACCESS_KEY=<your-secret-key>

    # 可选
    AWS_SESSION_TOKEN=<your-session-token>  # 用于临时凭据
    AWS_DEFAULT_REGION=<your-region>  # 默认为us-east-1
    ```

    **基本用法：**

    ```python Code theme={null}
    from crewai import LLM

    llm = LLM(
        model="bedrock/anthropic.claude-3-5-sonnet-20241022-v2:0",
        region_name="us-east-1"
    )
    ```

    **高级配置：**

    ```python Code theme={null}
    from crewai import LLM

    llm = LLM(
        model="bedrock/anthropic.claude-3-5-sonnet-20241022-v2:0",
        aws_access_key_id="your-access-key",  # 或设置 AWS_ACCESS_KEY_ID
        aws_secret_access_key="your-secret-key",  # 或设置 AWS_SECRET_ACCESS_KEY
        aws_session_token="your-session-token",  # 用于临时凭据
        region_name="us-east-1",
        temperature=0.7,
        max_tokens=4096,
        top_p=0.9,
        top_k=250,  # 用于Claude模型
        stop_sequences=["END", "STOP"],
        stream=True,  # 启用流式传输
        guardrail_config={  # 可选内容过滤
            "guardrailIdentifier": "your-guardrail-id",
            "guardrailVersion": "1"
        },
        additional_model_request_fields={  # 模型特定参数
            "top_k": 250
        }
    )
    ```

    **支持的环境变量：**

    * `AWS_ACCESS_KEY_ID`：AWS访问密钥（必需）
    * `AWS_SECRET_ACCESS_KEY`：AWS秘密密钥（必需）
    * `AWS_SESSION_TOKEN`：用于临时凭据的AWS会话令牌（可选）
    * `AWS_DEFAULT_REGION`：AWS区域（默认为`us-east-1`）

    **功能：**

    * 通过Converse API的原生工具调用支持
    * 流式和非流式响应
    * 具有重试逻辑的全面错误处理
    * 内容过滤的Guardrail配置
    * 通过`additional_model_request_fields`的模型特定参数
    * 令牌使用跟踪和停止原因日志记录
    * 支持所有Bedrock基础模型
    * 自动对话格式处理

    **重要说明：**

    * 使用现代Converse API进行统一模型访问
    * 自动处理模型特定对话要求
    * 系统消息与对话分开处理
    * 第一条消息必须来自用户（自动处理）
    * 某些模型（如Cohere）要求对话以用户消息结束

    [Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/models-regions.html)是一个托管服务，通过统一API提供对顶级AI公司多个基础模型的访问。

    | 模型 | 上下文窗口 | 最适用于 |
    | ----------------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------- |
    | Amazon Nova Pro | 高达300k令牌 | 高性能，在多样化任务中平衡准确性、速度和成本效益的模型。 |
    | Amazon Nova Micro | 高达128k令牌 | 高性能、成本效益的纯文本模型，针对最低延迟响应进行了优化。 |
    | Amazon Nova Lite | 高达300k令牌 | 高性能、负担得起的多模态处理，用于图像、视频和文本，具有实时功能。 |
    | Claude 3.7 Sonnet | 高达128k令牌 | 高性能，最适合复杂推理、编码和AI智能体 |
    | Claude 3.5 Sonnet v2 | 高达200k令牌 | 专门从事软件工程、智能体能力和计算机交互的最先进模型，成本优化。 |
    | Claude 3.5 Sonnet | 高达200k令牌 | 高性能模型，在多样化任务中提供卓越智能和推理，具有最佳速度成本平衡。 |
    | Claude 3.5 Haiku | 高达200k令牌 | 快速、紧凑的多模态模型，针对快速响应和无缝类人交互进行了优化 |
    | Claude 3 Sonnet | 高达200k令牌 | 平衡智能和速度的多模态模型，适用于高容量部署。 |
    | Claude 3 Haiku | 高达200k令牌 | 紧凑、高速多模态模型，针对快速响应和自然对话交互进行了优化 |
    | Claude 3 Opus | 高达200k令牌 | 最先进的多模态模型，在复杂任务、类人推理和卓越上下文理解方面表现出色。 |
    | Claude 2.1 | 高达200k令牌 | 增强版本，具有扩展上下文窗口、提高可靠性和减少幻觉，适用于长篇和RAG应用 |
    | Claude | 高达100k令牌 | 在复杂对话、创意内容和精确指令执行方面表现出色的多功能模型。 |
    | Claude Instant | 高达100k令牌 | 快速、成本效益的日常任务模型，如对话、分析、摘要和文档问答 |
    | Llama 3.1 405B Instruct | 高达128k令牌 | 用于合成数据生成、蒸馏和聊天机器人、编码和领域特定任务推理的高级LLM。 |
    | Llama 3.1 70B Instruct | 高达128k令牌 | 以卓越上下文理解、推理和文本生成支持复杂对话。 |
    | Llama 3.1 8B Instruct | 高达128k令牌 | 具有语言理解、卓越推理和文本生成功能的先进最先进模型。 |
    | Llama 3 70B Instruct | 高达8k令牌 | 以卓越上下文理解、推理和文本生成支持复杂对话。 |
    | Llama 3 8B Instruct | 高达8k令牌 | 具有语言理解、卓越推理和文本生成功能的先进最先进LLM。 |
    | Titan Text G1 - Lite | 高达4k令牌 | 轻量级、成本效益模型，针对英语任务和微调进行了优化，专注于摘要和内容生成。 |
    | Titan Text G1 - Express | 高达8k令牌 | 通用语言任务、聊天和RAG应用程序的通用模型，支持英语和100多种语言。 |
    | Cohere Command | 高达4k令牌 | 专门遵循用户命令和提供实用企业解决方案的模型。 |
    | Jurassic-2 Mid | 高达8,191令牌 | 平衡质量和负担能力的多样化语言任务模型，如问答、摘要和内容生成。 |
    | Jurassic-2 Ultra | 高达8,191令牌 | 高级文本生成和理解模型，在分析和内容创建等复杂任务中表现出色。 |
    | Jamba-Instruct | 高达256k令牌 | 具有扩展上下文窗口的模型，针对成本效益的文本生成、摘要和问答进行了优化。 |
    | Mistral 7B Instruct | 高达32k令牌 | 遵循指令、完成请求和生成创意文本的LLM。 |
    | Mistral 8x7B Instruct | 高达32k令牌 | 遵循指令、完成请求和生成创意文本的MOE LLM。 |
    | DeepSeek R1 | 32,768令牌 | 高级推理模型 |

    **注意：**要使用AWS Bedrock，请安装所需的依赖项：

    ```bash  theme={null}
    uv add "crewai[bedrock]"
    ```
  </Accordion>

  <Accordion title="Amazon SageMaker">
    ```toml Code theme={null}
    AWS_ACCESS_KEY_ID=<your-access-key>
    AWS_SECRET_ACCESS_KEY=<your-secret-key>
    AWS_DEFAULT_REGION=<your-region>
    ```

    在您的CrewAI项目中的使用示例：

    ```python Code theme={null}
    llm = LLM(
        model="sagemaker/<my-endpoint>"
    )
    ```
  </Accordion>

  <Accordion title="Mistral">
    在您的`.env`文件中设置以下环境变量：

    ```toml Code theme={null}
    MISTRAL_API_KEY=<your-api-key>
    ```

    在您的CrewAI项目中的使用示例：

    ```python Code theme={null}
    llm = LLM(
        model="mistral/mistral-large-latest",
        temperature=0.7
    )
    ```
  </Accordion>

  <Accordion title="Nvidia NIM">
    在您的`.env`文件中设置以下环境变量：

    ```toml Code theme={null}
    NVIDIA_API_KEY=<your-api-key>
    ```

    在您的CrewAI项目中的使用示例：

    ```python Code theme={null}
    llm = LLM(
        model="nvidia_nim/meta/llama3-70b-instruct",
        temperature=0.7
    )
    ```

    Nvidia NIM为各种用例提供全面的模型套件，从通用任务到专门应用程序。

    | 模型 | 上下文窗口 | 最适用于 |
    | ------------------------------------------- | -------------- | --------------------------------------------------------------------------------------------------------------------------- |
    | nvidia/mistral-nemo-minitron-8b-8k-instruct | 8,192令牌 | 最先进的小型语言模型，为聊天机器人、虚拟助手和内容生成提供卓越准确性。 |
    | nvidia/nemotron-4-mini-hindi-4b-instruct | 4,096令牌 | 专门针对印地语的双语印地语-英语SLM，用于设备上推理。 |
    | nvidia/llama-3.1-nemotron-70b-instruct | 128k令牌 | 为响应中的增强帮助而定制 |
    | nvidia/llama3-chatqa-1.5-8b | 128k令牌 | 为聊天机器人和搜索引擎生成高质量、上下文感知响应的高级LLM。 |
    | nvidia/llama3-chatqa-1.5-70b | 128k令牌 | 为聊天机器人和搜索引擎生成高质量、上下文感知响应的高级LLM。 |
    | nvidia/vila | 128k令牌 | 理解文本/图像/视频并创建信息性响应的多模态视觉语言模型 |
    | nvidia/neva-22 | 4,096令牌 | 理解文本/图像并生成信息性响应的多模态视觉语言模型 |
    | nvidia/nemotron-mini-4b-instruct | 8,192令牌 | 通用任务 |
    | nvidia/usdcode-llama3-70b-instruct | 128k令牌 | 回答OpenUSD知识查询和生成USD-Python代码的最先进LLM。 |
    | nvidia/nemotron-4-340b-instruct | 4,096令牌 | 创建模仿真实世界数据特征的多样化合成数据。 |
    | meta/codellama-70b | 100k令牌 | 能够从自然语言生成代码，反之亦然的LLM。 |
    | meta/llama2-70b | 4,096令牌 | 能够响应提示生成文本和代码的尖端大型AI语言模型。 |
    | meta/llama3-8b-instruct | 8,192令牌 | 具有语言理解、卓越推理和文本生成功能的最先进LLM。 |
    | meta/llama3-70b-instruct | 8,192令牌 | 以卓越上下文理解、推理和文本生成支持复杂对话。 |
    | meta/llama-3.1-8b-instruct | 128k令牌 | 具有语言理解、卓越推理和文本生成功能的最先进模型。 |
    | meta/llama-3.1-70b-instruct | 128k令牌 | 以卓越上下文理解、推理和文本生成支持复杂对话。 |
    | meta/llama-3.1-405b-instruct | 128k令牌 | 用于合成数据生成、蒸馏和聊天机器人、编码和领域特定任务推理的高级LLM。 |
    | meta/llama-3.2-1b-instruct | 128k令牌 | 具有语言理解、卓越推理和文本生成功能的最先进小型语言模型。 |
    | meta/llama-3.2-3b-instruct | 128k令牌 | 具有语言理解、卓越推理和文本生成功能的最先进小型语言模型。 |
    | meta/llama-3.2-11b-vision-instruct | 128k令牌 | 具有语言理解、卓越推理和文本生成功能的最先进小型语言模型。 |
    | meta/llama-3.2-90b-vision-instruct | 128k令牌 | 具有语言理解、卓越推理和文本生成功能的最先进小型语言模型。 |
    | google/gemma-7b | 8,192令牌 | 尖端文本生成模型，文本理解、转换和代码生成。 |
    | google/gemma-2b | 8,192令牌 | 尖端文本生成模型，文本理解、转换和代码生成。 |
    | google/codegemma-7b | 8,192令牌 | 基于Google的Gemma-7b构建的尖端模型，专门用于代码生成和代码补全。 |
    | google/codegemma-1.1-7b | 8,192令牌 | 用于代码生成、补全、推理和指令遵循的高级编程模型。 |
    | google/recurrentgemma-2b | 8,192令牌 | 基于新颖循环架构的语言模型，用于生成长序列时的更快推理。 |
    | google/gemma-2-9b-it | 8,192令牌 | 尖端文本生成模型，文本理解、转换和代码生成。 |
    | google/gemma-2-27b-it | 8,192令牌 | 尖端文本生成模型，文本理解、转换和代码生成。 |
    | google/gemma-2-2b-it | 8,192令牌 | 尖端文本生成模型，文本理解、转换和代码生成。 |
    | google/deplot | 512令牌 | 一次性视觉语言理解模型，将图表图像转换为表格。 |
    | google/paligemma | 8,192令牌 | 擅长理解文本和视觉输入以产生信息性响应的视觉语言模型。 |
    | mistralai/mistral-7b-instruct-v0.2 | 32k令牌 | 遵循指令、完成请求和生成创意文本的LLM。 |
    | mistralai/mixtral-8x7b-instruct-v0.1 | 8,192令牌 | 遵循指令、完成请求和生成创意文本的MOE LLM。 |
    | mistralai/mistral-large | 4,096令牌 | 创建模仿真实世界数据特征的多样化合成数据。 |
    | mistralai/mixtral-8x22b-instruct-v0.1 | 8,192令牌 | 创建模仿真实世界数据特征的多样化合成数据。 |
    | mistralai/mistral-7b-instruct-v0.3 | 32k令牌 | 遵循指令、完成请求和生成创意文本的LLM。 |
    | nv-mistralai/mistral-nemo-12b-instruct | 128k令牌 | 用于推理、编码、多语言任务的最先进语言模型；在单个GPU上运行。 |
    | mistralai/mamba-codestral-7b-v0.1 | 256k令牌 | 用于在各种编程语言和任务中编写和交互代码的模型。 |
    | microsoft/phi-3-mini-128k-instruct | 128K令牌 | 具有强大数学和逻辑推理技能的轻量级、最先进开放LLM。 |
    | microsoft/phi-3-mini-4k-instruct | 4,096令牌 | 具有强大数学和逻辑推理技能的轻量级、最先进开放LLM。 |
    | microsoft/phi-3-small-8k-instruct | 8,192令牌 | 具有强大数学和逻辑推理技能的轻量级、最先进开放LLM。 |
    | microsoft/phi-3-small-128k-instruct | 128K令牌 | 具有强大数学和逻辑推理技能的轻量级、最先进开放LLM。 |
    | microsoft/phi-3-medium-4k-instruct | 4,096令牌 | 具有强大数学和逻辑推理技能的轻量级、最先进开放LLM。 |
    | microsoft/phi-3-medium-128k-instruct | 128K令牌 | 具有强大数学和逻辑推理技能的轻量级、最先进开放LLM。 |
    | microsoft/phi-3.5-mini-instruct | 128K令牌 | 为延迟受限、内存/计算受限环境中的AI应用程序提供支持的多语言轻量级LLM |
    | microsoft/phi-3.5-moe-instruct | 128K令牌 | 基于专家混合架构的高级LLM，用于提供计算高效的内容生成 |
    | microsoft/kosmos-2 | 1,024令牌 | 突破性的多模态模型，旨在理解和推理图像中的视觉元素。 |
    | microsoft/phi-3-vision-128k-instruct | 128k令牌 | 在从图像进行高质量推理方面表现出色的尖端开放多模态模型。 |
    | microsoft/phi-3.5-vision-instruct | 128k令牌 | 在从图像进行高质量推理方面表现出色的尖端开放多模态模型。 |
    | databricks/dbrx-instruct | 12k令牌 | 在语言理解、编码和RAG方面具有最先进性能的通用LLM。 |
    | snowflake/arctic | 1,024令牌 | 为专注于SQL生成和编码的企业应用程序提供高效推理。 |
    | aisingapore/sea-lion-7b-instruct | 4,096令牌 | 代表和服务于东南亚语言和文化多样性的LLM |
    | ibm/granite-8b-code-instruct | 4,096令牌 | 用于代码生成、补全、解释和多轮转换的软件编程LLM。 |
    | ibm/granite-34b-code-instruct | 8,192令牌 | 用于代码生成、补全、解释和多轮转换的软件编程LLM。 |
    | ibm/granite-3.0-8b-instruct | 4,096令牌 | 支持RAG、摘要、分类、编码和智能体AI的高级小型语言模型 |
    | ibm/granite-3.0-3b-a800m-instruct | 4,096令牌 | 用于RAG、摘要、实体提取和分类的高效专家混合模型 |
    | mediatek/breeze-7b-instruct | 4,096令牌 | 创建模仿真实世界数据特征的多样化合成数据。 |
    | upstage/solar-10.7b-instruct | 4,096令牌 | 在NLP任务中表现出色，特别是在指令遵循、推理和数学方面。 |
    | writer/palmyra-med-70b-32k | 32k令牌 | 在医学领域提供准确、上下文相关响应的领先LLM。 |
    | writer/palmyra-med-70b | 32k令牌 | 在医学领域提供准确、上下文相关响应的领先LLM。 |
    | writer/palmyra-fin-70b-32k | 32k令牌 | 专门用于财务分析、报告和数据处理的LLM |
    | 01-ai/yi-large | 32k令牌 | 在英语和中文上训练的强大模型，用于包括聊天机器人和创意写作在内的多样化任务。 |
    | deepseek-ai/deepseek-coder-6.7b-instruct | 2k令牌 | 在代码生成、补全和填充方面提供高级功能的强大编码模型 |
    | rakuten/rakutenai-7b-instruct | 1,024令牌 | 具有语言理解、卓越推理和文本生成功能的最先进LLM。 |
    | rakuten/rakutenai-7b-chat | 1,024令牌 | 具有语言理解、卓越推理和文本生成功能的最先进LLM。 |
    | baichuan-inc/baichuan2-13b-chat | 4,096令牌 | 支持中英文聊天、编码、数学、指令遵循、解决测验 |
  </Accordion>

  <Accordion title="使用WSL2在本地部署的NVIDIA NIM">
    NVIDIA NIM使您能够使用WSL2（Windows子系统Linux）在Windows机器上本地运行强大的LLM。
    这种方法允许您利用NVIDIA GPU进行私有、安全和成本效益的AI推理，而无需依赖云服务。
    非常适合需要数据隐私或离线功能的开发、测试或生产场景。

    设置本地NVIDIA NIM模型的分步指南：

    1. 遵循[NVIDIA网站](https://docs.nvidia.com/nim/wsl2/latest/getting-started.html)的安装说明

    2. 安装本地模型。对于Llama 3.1-8b，请遵循[说明](https://build.nvidia.com/meta/llama-3_1-8b-instruct/deploy)

    3. 配置您的crewai本地模型：

    ```python Code theme={null}
    from crewai.llm import LLM

    local_nvidia_nim_llm = LLM(
        model="openai/meta/llama-3.1-8b-instruct", # 它是一个openai-api兼容模型
        base_url="http://localhost:8000/v1",
        api_key="<your_api_key|any text if you have not configured it>", # api_key是必需的，但您可以使用任何文本
    )

    # 然后您可以在您的crew中使用它：

    @CrewBase
    class MyCrew():
        # ...

        @agent
        def researcher(self) -> Agent:
            return Agent(
                config=self.agents_config['researcher'], # type: ignore[index]
                llm=local_nvidia_nim_llm
            )

        # ...
    ```
  </Accordion>

  <Accordion title="Groq">
    在您的`.env`文件中设置以下环境变量：

    ```toml Code theme={null}
    GROQ_API_KEY=<your-api-key>
    ```

    在您的CrewAI项目中的使用示例：

    ```python Code theme={null}
    llm = LLM(
        model="groq/llama-3.2-90b-text-preview",
        temperature=0.7
    )
    ```

    | 模型 | 上下文窗口 | 最适用于 |
    | ---------------- | -------------- | ------------------------------------- |
    | Llama 3.1 70B/8B | 131,072令牌 | 高性能、大上下文任务 |
    | Llama 3.2系列 | 8,192令牌 | 通用任务 |
    | Mixtral 8x7B | 32,768令牌 | 平衡性能和上下文 |
  </Accordion>

  <Accordion title="IBM watsonx.ai">
    在您的`.env`文件中设置以下环境变量：

    ```toml Code theme={null}
    # 必需
    WATSONX_URL=<your-url>
    WATSONX_APIKEY=<your-apikey>
    WATSONX_PROJECT_ID=<your-project-id>

    # 可选
    WATSONX_TOKEN=<your-token>
    WATSONX_DEPLOYMENT_SPACE_ID=<your-space-id>
    ```

    在您的CrewAI项目中的使用示例：

    ```python Code theme={null}
    llm = LLM(
        model="watsonx/meta-llama/llama-3-1-70b-instruct",
        base_url="https://api.watsonx.ai/v1"
    )
    ```
  </Accordion>

  <Accordion title="Ollama (本地LLMs)">
    1. 安装Ollama：[ollama.ai](https://ollama.ai/)
    2. 运行模型：`ollama run llama3`
    3. 配置：

    ```python Code theme={null}
    llm = LLM(
        model="ollama/llama3:70b",
        base_url="http://localhost:11434"
    )
    ```
  </Accordion>

  <Accordion title="Fireworks AI">
    在您的`.env`文件中设置以下环境变量：

    ```toml Code theme={null}
    FIREWORKS_API_KEY=<your-api-key>
    ```

    在您的CrewAI项目中的使用示例：

    ```python Code theme={null}
    llm = LLM(
        model="fireworks_ai/accounts/fireworks/models/llama-v3-70b-instruct",
        temperature=0.7
    )
    ```
  </Accordion>

  <Accordion title="Perplexity AI">
    在您的`.env`文件中设置以下环境变量：

    ```toml Code theme={null}
    PERPLEXITY_API_KEY=<your-api-key>
    ```

    在您的CrewAI项目中的使用示例：

    ```python Code theme={null}
    llm = LLM(
        model="llama-3.1-sonar-large-128k-online",
        base_url="https://api.perplexity.ai/"
    )
    ```
  </Accordion>

  <Accordion title="Hugging Face">
    在您的`.env`文件中设置以下环境变量：

    ```toml Code theme={null}
    HF_TOKEN=<your-api-key>
    ```

    在您的CrewAI项目中的使用示例：

    ```python Code theme={null}
    llm = LLM(
        model="huggingface/meta-llama/Meta-Llama-3.1-8B-Instruct"
    )
    ```
  </Accordion>

  <Accordion title="SambaNova">
    在您的`.env`文件中设置以下环境变量：

    ```toml Code theme={null}
    SAMBANOVA_API_KEY=<your-api-key>
    ```

    在您的CrewAI项目中的使用示例：

    ```python Code theme={null}
    llm = LLM(
        model="sambanova/Meta-Llama-3.1-8B-Instruct",
        temperature=0.7
    )
    ```

    | 模型 | 上下文窗口 | 最适用于 |
    | ---------------- | -------------------- | ------------------------------------- |
    | Llama 3.1 70B/8B | 高达131,072令牌 | 高性能、大上下文任务 |
    | Llama 3.1 405B | 8,192令牌 | 高性能和输出质量 |
    | Llama 3.2系列 | 8,192令牌 | 通用、多模态任务 |
    | Llama 3.3 70B | 高达131,072令牌 | 高性能和输出质量 |
    | Qwen2系列 | 8,192令牌 | 高性能和输出质量 |
  </Accordion>

  <Accordion title="Cerebras">
    在您的`.env`文件中设置以下环境变量：

    ```toml Code theme={null}
    # 必需
    CEREBRAS_API_KEY=<your-api-key>
    ```

    在您的CrewAI项目中的使用示例：

    ```python Code theme={null}
    llm = LLM(
        model="cerebras/llama3.1-70b",
        temperature=0.7,
        max_tokens=8192
    )
    ```

    <Info>
      Cerebras功能：

      * 快速推理速度
      * 竞争性定价
      * 速度和质量的良好平衡
      * 支持长上下文窗口
    </Info>
  </Accordion>

  <Accordion title="Open Router">
    在您的`.env`文件中设置以下环境变量：

    ```toml Code theme={null}
    OPENROUTER_API_KEY=<your-api-key>
    ```

    在您的CrewAI项目中的使用示例：

    ```python Code theme={null}
    llm = LLM(
        model="openrouter/deepseek/deepseek-r1",
        base_url="https://openrouter.ai/api/v1",
        api_key=OPENROUTER_API_KEY
    )
    ```

    <Info>
      Open Router模型：

      * openrouter/deepseek/deepseek-r1
      * openrouter/deepseek/deepseek-chat
    </Info>
  </Accordion>

  <Accordion title="Nebius AI Studio">
    在您的`.env`文件中设置以下环境变量：

    ```toml Code theme={null}
    NEBIUS_API_KEY=<your-api-key>
    ```

    在您的CrewAI项目中的使用示例：

    ```python Code theme={null}
    llm = LLM(
        model="nebius/Qwen/Qwen3-30B-A3B"
    )
    ```

    <Info>
      Nebius AI Studio功能：

      * 大量开源模型集合
      * 更高的速率限制
      * 竞争性定价
      * 速度和质量的良好平衡
    </Info>
  </Accordion>
</AccordionGroup>

## 流式响应

CrewAI支持来自LLM的流式响应，允许您的应用程序在生成输出时实时接收和处理输出。

<Tabs>
  <Tab title="基本设置">
    在初始化LLM时通过将`stream`参数设置为`True`来启用流式传输：

    ```python  theme={null}
    from crewai import LLM

    # 创建启用流式传输的LLM
    llm = LLM(
        model="openai/gpt-4o",
        stream=True  # 启用流式传输
    )
    ```

    启用流式传输时，响应在生成时以块的形式交付，创建更响应式的用户体验。
  </Tab>

  <Tab title="事件处理">
    CrewAI在流式传输过程中为每个接收到的块发出事件：

    ```python  theme={null}
    from crewai.events import (
      LLMStreamChunkEvent
    )
    from crewai.events import BaseEventListener

    class MyCustomListener(BaseEventListener):
        def setup_listeners(self, crewai_event_bus):
            @crewai_event_bus.on(LLMStreamChunkEvent)
            def on_llm_stream_chunk(self, event: LLMStreamChunkEvent):
              # 在每个块到达时处理
              print(f"接收到的块: {event.chunk}")

    my_listener = MyCustomListener()
    ```

    <Tip>
      [点击此处](https://docs.crewai.com/concepts/event-listener#event-listeners)了解更多详情
    </Tip>
  </Tab>

  <Tab title="智能体和任务跟踪">
    CrewAI中的所有LLM事件都包含智能体和任务信息，允许您按特定智能体或任务跟踪和过滤LLM交互：

    ```python  theme={null}
    from crewai import LLM, Agent, Task, Crew
    from crewai.events import LLMStreamChunkEvent
    from crewai.events import BaseEventListener

    class MyCustomListener(BaseEventListener):
        def setup_listeners(self, crewai_event_bus):
            @crewai_event_bus.on(LLMStreamChunkEvent)
            def on_llm_stream_chunk(source, event):
                if researcher.id == event.agent_id:
                    print("\n==============\n 获取事件:", event, "\n==============\n")


    my_listener = MyCustomListener()

    llm = LLM(model="gpt-4o-mini", temperature=0, stream=True)

    researcher = Agent(
        role="关于用户",
        goal="您了解用户的一切。",
        backstory="""您是理解人们及其偏好的大师。""",
        llm=llm,
    )

    search = Task(
        description="回答以下关于用户的问题：{question}",
        expected_output="问题的答案。",
        agent=researcher,
    )

    crew = Crew(agents=[researcher], tasks=[search])

    result = crew.kickoff(
        inputs={"question": "..."}
    )
    ```

    <Info>
      此功能特别适用于：

      * 调试特定智能体行为
      * 按任务类型记录LLM使用情况
      * 审计哪些智能体正在进行什么类型的LLM调用
      * 特定任务的性能监控
    </Info>
  </Tab>
</Tabs>

## 结构化LLM调用

CrewAI允许您使用Pydantic模型定义`response_format`，从而支持来自LLM调用的结构化响应。这使得框架能够自动解析和验证输出，更容易地将响应集成到您的应用程序中，而无需手动后处理。

例如，您可以定义一个Pydantic模型来表示预期的响应结构，并在实例化LLM时将其作为`response_format`传递。然后，模型将用于将LLM输出转换为结构化的Python对象。

```python Code theme={null}
from crewai import LLM

class Dog(BaseModel):
    name: str
    age: int
    breed: str


llm = LLM(model="gpt-4o", response_format=Dog)

response = llm.call(
    "分析以下消息并返回名称、年龄和品种。"
    "这是Kona！她3岁，是一只黑色德国牧羊犬。"
)
print(response)

# 输出:
# Dog(name='Kona', age=3, breed='black german shepherd')
```

## 高级功能和优化

了解如何充分利用您的LLM配置：

<AccordionGroup>
  <Accordion title="上下文窗口管理">
    CrewAI包括智能上下文管理功能：

    ```python  theme={null}
    from crewai import LLM

    # CrewAI自动处理：
    # 1. 令牌计数和跟踪
    # 2. 必要时的内容摘要
    # 3. 大上下文的任务拆分

    llm = LLM(
        model="gpt-4",
        max_tokens=4000,  # 限制响应长度
    )
    ```

    <Info>
      上下文管理最佳实践：

      1. 为您的任务选择具有适当上下文窗口的模型
      2. 尽可能预处理长输入
      3. 对大文档使用分块
      4. 监控令牌使用以优化成本
    </Info>
  </Accordion>

  <Accordion title="性能优化">
    <Steps>
      <Step title="令牌使用优化">
        为您的任务选择正确的上下文窗口：

        * 小任务（最多4K令牌）：标准模型
        * 中等任务（4K-32K之间）：增强模型
        * 大任务（超过32K）：大上下文模型

        ```python  theme={null}
        # 使用适当设置配置模型
        llm = LLM(
            model="openai/gpt-4-turbo-preview",
            temperature=0.7,    # 根据任务调整
            max_tokens=4096,    # 根据输出需求设置
            timeout=300        # 复杂任务的更长超时
        )
        ```

        <Tip>
          * 事实响应使用较低温度（0.1到0.3）
          * 创意任务使用较高温度（0.7到0.9）
        </Tip>
      </Step>

      <Step title="最佳实践">
        1. 监控令牌使用情况
        2. 实施速率限制
        3. 尽可能使用缓存
        4. 设置适当的max_tokens限制
      </Step>
    </Steps>

    <Info>
      记得定期监控您的令牌使用情况，并根据需要调整您的配置以优化成本和性能。
    </Info>
  </Accordion>

  <Accordion title="删除额外参数">
    CrewAI内部使用原生sdk进行LLM调用，这允许您删除特定用例中不需要的额外参数。这可以帮助简化代码并减少LLM配置的复杂性。
    例如，如果您不需要发送<code>stop</code>参数，您可以简单地在LLM调用中省略它：

    ```python  theme={null}
    from crewai import LLM
    import os

    os.environ["OPENAI_API_KEY"] = "<api-key>"

    o3_llm = LLM(
        model="o3",
        drop_params=True,
        additional_drop_params=["stop"]
    )
    ```
  </Accordion>
</AccordionGroup>

## 常见问题和解决方案

<Tabs>
  <Tab title="身份验证">
    <Warning>
      大多数身份验证问题可以通过检查API密钥格式和环境变量名称来解决。
    </Warning>

    ```bash  theme={null}
    # OpenAI
    OPENAI_API_KEY=sk-...

    # Anthropic
    ANTHROPIC_API_KEY=sk-ant-...
    ```
  </Tab>

  <Tab title="模型名称">
    <Check>
      始终在模型名称中包含提供者前缀
    </Check>

    ```python  theme={null}
    # 正确
    llm = LLM(model="openai/gpt-4")

    # 错误
    llm = LLM(model="gpt-4")
    ```
  </Tab>

  <Tab title="上下文长度">
    <Tip>
      为广泛任务使用更大的上下文模型
    </Tip>

    ```python  theme={null}
    # 大上下文模型
    llm = LLM(model="openai/gpt-4o")  # 128K令牌
    ```
  </Tab>
</Tabs>