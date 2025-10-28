# Apify Actors

> `ApifyActorsTool` 可让您调用 Apify Actors，为您的 CrewAI 工作流程提供网页抓取、爬取、数据提取和网页自动化功能。

# `ApifyActorsTool`

将 [Apify Actors](https://apify.com/actors) 集成到您的 CrewAI 工作流程中。

## 描述

`ApifyActorsTool` 将 [Apify Actors](https://apify.com/actors)（基于云的网页抓取和自动化程序）连接到您的 CrewAI 工作流程。
您可以使用 [Apify Store](https://apify.com/store) 中的 4000 多个 Actors，用于从社交媒体、搜索引擎、在线地图、电子商务网站、旅游门户网站或一般网站提取数据等用例。

详情请参阅 Apify 文档中的 [Apify CrewAI 集成](https://docs.apify.com/platform/integrations/crewai)。

## 入门步骤

<Steps>
  <Step title="安装依赖项">
    使用 pip 安装 `crewai[tools]` 和 `langchain-apify`：`pip install 'crewai[tools]' langchain-apify`。
  </Step>
  <Step title="获取 Apify API 令牌">
    注册 [Apify Console](https://console.apify.com/) 并获取您的 [Apify API 令牌](https://console.apify.com/settings/integrations)。
  </Step>
  <Step title="配置环境">
    将您的 Apify API 令牌设置为 `APIFY_API_TOKEN` 环境变量，以启用该工具的功能。
  </Step>
</Steps>

## 使用示例

手动使用 `ApifyActorsTool` 运行 [RAG Web Browser Actor](https://apify.com/apify/rag-web-browser) 来执行网络搜索：

```python  theme={null}
from crewai_tools import ApifyActorsTool

# 使用 Apify Actor 初始化工具
tool = ApifyActorsTool(actor_name="apify/rag-web-browser")

# 使用输入参数运行工具
results = tool.run(run_input={"query": "什么是 CrewAI？", "maxResults": 5})

# 处理结果
for result in results:
    print(f"URL: {result['metadata']['url']}")
    print(f"内容: {result.get('markdown', 'N/A')[:100]}...")
```

### 预期输出

以下是运行上述代码的输出：

```text  theme={null}
URL: https://www.example.com/crewai-intro
内容: CrewAI 是一个用于构建 AI 驱动工作流程的框架...
URL: https://docs.crewai.com/
内容: CrewAI 的官方文档...
```

`ApifyActorsTool` 会自动使用提供的 `actor_name` 从 Apify 获取 Actor 定义和输入模式，然后构建工具描述和参数模式。这意味着您只需指定有效的 `actor_name`，当与代理一起使用时，工具会处理其余部分 - 无需指定 `run_input`。它的工作原理如下：

```python  theme={null}
from crewai import Agent
from crewai_tools import ApifyActorsTool

rag_browser = ApifyActorsTool(actor_name="apify/rag-web-browser")

agent = Agent(
    role="研究分析师",
    goal="查找和总结有关特定主题的信息",
    backstory="您是一名注重细节的资深研究员",
    tools=[rag_browser],
)
```

您可以通过更改 `actor_name` 来运行 [Apify Store](https://apify.com/store) 中的其他 Actors，并在手动使用时根据 Actor 输入模式调整 `run_input`。

有关与代理一起使用的示例，请参阅 [CrewAI Actor 模板](https://apify.com/templates/python-crewai)。

## 配置

`ApifyActorsTool` 需要以下输入才能工作：

* **`actor_name`**
  要运行的 Apify Actor 的 ID，例如 `"apify/rag-web-browser"`。在 [Apify Store](https://apify.com/store) 中浏览所有 Actors。
* **`run_input`**
  手动运行工具时 Actor 的输入参数字典。
  * 例如，对于 `apify/rag-web-browser` Actor：`{"query": "搜索词", "maxResults": 5}`
  * 有关输入参数列表，请参阅 Actor 的[输入模式](https://apify.com/apify/rag-web-browser/input-schema)。

## 资源

* **[Apify](https://apify.com/)**：探索 Apify 平台。
* **[如何在 Apify 上构建 AI 代理](https://blog.apify.com/how-to-build-an-ai-agent/)** - 在 Apify 平台上创建、发布和变现 AI 代理的完整分步指南。
* **[RAG Web Browser Actor](https://apify.com/apify/rag-web-browser)**：一种用于 LLM 网络搜索的流行 Actor。
* **[CrewAI 集成指南](https://docs.apify.com/platform/integrations/crewai)**：遵循 Apify 和 CrewAI 集成的官方指南。