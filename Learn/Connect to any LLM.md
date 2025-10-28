# 连接到任何大语言模型

> 关于使用LiteLLM将CrewAI与各种大语言模型(LLM)集成的综合指南，包括支持的提供商和配置选项。

## 将CrewAI连接到LLM

CrewAI使用LiteLLM连接到各种各样的语言模型(LLM)。这种集成提供了广泛的多功能性，允许您通过简单、统一的接口使用来自众多提供商的模型。

<Note>
  默认情况下，CrewAI使用`gpt-4o-mini`模型。这是由`OPENAI_MODEL_NAME`环境变量决定的，如果未设置，该变量默认为"gpt-4o-mini"。
  您可以按照本指南中所述，轻松配置您的代理使用不同的模型或提供商。
</Note>

## 支持的提供商

LiteLLM支持广泛的提供商，包括但不限于：

* OpenAI
* Anthropic
* Google (Vertex AI, Gemini)
* Azure OpenAI
* AWS (Bedrock, SageMaker)
* Cohere
* VoyageAI
* Hugging Face
* Ollama
* Mistral AI
* Replicate
* Together AI
* AI21
* Cloudflare Workers AI
* DeepInfra
* Groq
* SambaNova
* Nebius AI Studio
* [NVIDIA NIMs](https://docs.api.nvidia.com/nim/reference/models-1)
* 以及更多！

有关支持提供商的完整和最新列表，请参阅[LiteLLM提供商文档](https://docs.litellm.ai/docs/providers)。

## 更改LLM

要为您的CrewAI代理使用不同的LLM，您有几种选择：

<Tabs>
  <Tab title="使用字符串标识符">
    在初始化代理时将模型名称作为字符串传递：

    <CodeGroup>
      ```python Code theme={null}
      from crewai import Agent

      # 使用OpenAI的GPT-4
      openai_agent = Agent(
          role='OpenAI专家',
          goal='使用GPT-4提供见解',
          backstory="由OpenAI最新模型驱动的AI助手。",
          llm='gpt-4'
      )

      # 使用Anthropic的Claude
      claude_agent = Agent(
          role='Anthropic专家',
          goal='使用Claude分析数据',
          backstory="利用Anthropic语言模型的AI助手。",
          llm='claude-2'
      )
      ```
    </CodeGroup>
  </Tab>

  <Tab title="使用LLM类">
    对于更详细的配置，请使用LLM类：

    <CodeGroup>
      ```python Code theme={null}
      from crewai import Agent, LLM

      llm = LLM(
          model="gpt-4",
          temperature=0.7,
          base_url="https://api.openai.com/v1",
          api_key="your-api-key-here"
      )

      agent = Agent(
          role='定制LLM专家',
          goal='提供量身定制的响应',
          backstory="具有自定义LLM设置的AI助手。",
          llm=llm
      )
      ```
    </CodeGroup>
  </Tab>
</Tabs>

## 配置选项

在为代理配置LLM时，您可以访问各种参数：

| 参数                |        类型        | 描述                                                      |
| :----------------- | :----------------: | :------------------------------------------------------- |
| **model**          |        `str`       | 要使用的模型名称（例如，"gpt-4"、"claude-2"）            |
| **temperature**    |       `float`      | 控制输出的随机性（0.0到1.0）                             |
| **max_tokens**     |        `int`       | 要生成的最大令牌数                                       |
| **top_p**          |       `float`      | 控制输出的多样性（0.0到1.0）                             |
| **frequency_penalty** |       `float`      | 根据令牌在迄今为止文本中的频率对其进行惩罚               |
| **presence_penalty**  |       `float`      | 根据令牌在迄今为止文本中的存在对其进行惩罚               |
| **stop**           | `str`, `List[str]` | 停止生成的序列                                           |
| **base_url**       |        `str`       | API端点的基础URL                                         |
| **api_key**        |        `str`       | 用于身份验证的API密钥                                    |

有关参数的完整列表及其描述，请参阅LLM类文档。

## 连接到OpenAI兼容的LLM

您可以使用环境变量或在LLM类上设置特定属性来连接到OpenAI兼容的LLM：

<Tabs>
  <Tab title="使用环境变量">
    <CodeGroup>
      ```python Generic theme={null}
      import os

      os.environ["OPENAI_API_KEY"] = "your-api-key"
      os.environ["OPENAI_API_BASE"] = "https://api.your-provider.com/v1"
      os.environ["OPENAI_MODEL_NAME"] = "your-model-name"
      ```

      ```python Google theme={null}
      import os

      # 使用Gemini的OpenAI兼容API的示例。
      os.environ["OPENAI_API_KEY"] = "your-gemini-key"  # 应该以AIza开头...
      os.environ["OPENAI_API_BASE"] = "https://generativelanguage.googleapis.com/v1beta/openai/"
      os.environ["OPENAI_MODEL_NAME"] = "openai/gemini-2.0-flash"  # 在openai/下添加您的Gemini模型
      ```
    </CodeGroup>
  </Tab>

  <Tab title="使用LLM类属性">
    <CodeGroup>
      ```python Generic theme={null}
      llm = LLM(
          model="custom-model-name",
          api_key="your-api-key",
          base_url="https://api.your-provider.com/v1"
      )
      agent = Agent(llm=llm, ...)
      ```

      ```python Google theme={null}
      # 使用Gemini的OpenAI兼容API的示例
      llm = LLM(
          model="openai/gemini-2.0-flash",
          base_url="https://generativelanguage.googleapis.com/v1beta/openai/",
          api_key="your-gemini-key",  # 应该以AIza开头...
      )
      agent = Agent(llm=llm, ...)
      ```
    </CodeGroup>
  </Tab>
</Tabs>

## 使用Ollama的本地模型

对于像Ollama提供的本地模型：

<Steps>
  <Step title="下载并安装Ollama">
    [点击此处下载并安装Ollama](https://ollama.com/download)
  </Step>

  <Step title="拉取所需模型">
    例如，运行`ollama pull llama3.2`来下载模型。
  </Step>

  <Step title="配置您的代理">
    <CodeGroup>
      ```python Code theme={null}
          agent = Agent(
              role='本地AI专家',
              goal='使用本地模型处理信息',
              backstory="在本地硬件上运行的AI助手。",
              llm=LLM(model="ollama/llama3.2", base_url="http://localhost:11434")
          )
      ```
    </CodeGroup>
  </Step>
</Steps>

## 更改基础API URL

您可以通过设置`base_url`参数来更改任何LLM提供商的基础API URL：

```python Code theme={null}
llm = LLM(
    model="custom-model-name",
    base_url="https://api.your-provider.com/v1",
    api_key="your-api-key"
)
agent = Agent(llm=llm, ...)
```

当使用OpenAI兼容的API或当您需要为所选提供商指定不同的端点时，这特别有用。

## 结论

通过利用LiteLLM，CrewAI提供了与各种LLM的无缝集成。这种灵活性允许您为您的特定需求选择最合适的模型，无论您优先考虑性能、成本效益还是本地部署。请记住查阅[LiteLLM文档](https://docs.litellm.ai/docs/)以获取有关支持的模型和配置选项的最新信息。