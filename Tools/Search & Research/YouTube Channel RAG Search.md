# YouTube 频道 RAG 搜索

> `YoutubeChannelSearchTool` 旨在对 YouTube 频道的内容执行 RAG（检索增强生成）搜索。

# `YoutubeChannelSearchTool`

<Note>
  我们仍在努力改进工具，未来可能会出现意外行为或变更。
</Note>

## 描述

此工具旨在对特定 YouTube 频道的内容进行语义搜索。它利用 RAG（检索增强生成）方法，提供相关的搜索结果，对于在不手动筛选视频的情况下提取信息或查找特定内容非常有价值。它简化了在 YouTube 频道内的搜索过程，满足研究人员、内容创作者以及寻求特定信息或主题的观众的需求。

## 安装

要使用 `YoutubeChannelSearchTool`，必须安装 `crewai_tools` 包。在您的 shell 中执行以下命令进行安装：

```shell theme={null}
pip install 'crewai[tools]'
```

## 示例

以下示例演示了如何将 `YoutubeChannelSearchTool` 与 CrewAI 智能体一起使用：

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import YoutubeChannelSearchTool

# 初始化用于通用 YouTube 频道搜索的工具
youtube_channel_tool = YoutubeChannelSearchTool()

# 定义一个使用该工具的智能体
channel_researcher = Agent(
    role="频道研究员",
    goal="从 YouTube 频道中提取相关信息",
    backstory="一位专门分析 YouTube 频道内容的研究专家。",
    tools=[youtube_channel_tool],
    verbose=True,
)

# 在特定频道中搜索信息的示例任务
research_task = Task(
    description="在 YouTube 频道 {youtube_channel_handle} 中搜索有关机器学习教程的信息",
    expected_output="关于该频道上可用的关键机器学习教程的摘要。",
    agent=channel_researcher,
)

# 创建并运行团队
crew = Crew(agents=[channel_researcher], tasks=[research_task])
result = crew.kickoff(inputs={"youtube_channel_handle": "@exampleChannel"})
```

您也可以使用特定的 YouTube 频道句柄来初始化工具：

```python Code theme={null}
# 使用特定的 YouTube 频道句柄初始化工具
youtube_channel_tool = YoutubeChannelSearchTool(
    youtube_channel_handle='@exampleChannel'
)

# 定义一个使用该工具的智能体
channel_researcher = Agent(
    role="频道研究员",
    goal="从特定的 YouTube 频道中提取相关信息",
    backstory="一位专门分析 YouTube 频道内容的研究专家。",
    tools=[youtube_channel_tool],
    verbose=True,
)
```

## 参数

`YoutubeChannelSearchTool` 接受以下参数：

* **youtube\_channel\_handle**：可选。要搜索的 YouTube 频道句柄。如果在初始化时提供，智能体在使用该工具时将无需指定它。如果句柄不以“@”开头，将自动添加。
* **config**：可选。底层 RAG 系统的配置，包括 LLM 和嵌入模型设置。
* **summarize**：可选。是否对检索到的内容进行摘要。默认为 `False`。

当与智能体一起使用工具时，智能体需要提供：

* **search\_query**：必需。用于在频道内容中查找相关信息的搜索查询。
* **youtube\_channel\_handle**：仅在初始化时未提供时为必需。要搜索的 YouTube 频道句柄。

## 自定义模型和嵌入

默认情况下，该工具使用 OpenAI 进行嵌入和摘要。要自定义模型，您可以使用配置字典，如下所示：

```python Code theme={null}
youtube_channel_tool = YoutubeChannelSearchTool(
    config=dict(
        llm=dict(
            provider="ollama", # 或 google, openai, anthropic, llama2, ...
            config=dict(
                model="llama2",
                # temperature=0.5,
                # top_p=1,
                # stream=true,
            ),
        ),
        embedder=dict(
            provider="google", # 或 openai, ollama, ...
            config=dict(
                model="models/embedding-001",
                task_type="retrieval_document",
                # title="Embeddings",
            ),
        ),
    )
)
```

## 智能体集成示例

以下是一个更详细的示例，展示如何将 `YoutubeChannelSearchTool` 与 CrewAI 智能体集成：

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import YoutubeChannelSearchTool

# 初始化工具
youtube_channel_tool = YoutubeChannelSearchTool()

# 定义一个使用该工具的智能体
channel_researcher = Agent(
    role="频道研究员",
    goal="从 YouTube 频道中提取和分析信息",
    backstory="""你是一位专业的频道研究员，专门从事从 YouTube 频道中提取
    和分析信息。你注重细节，能够快速识别整个频道视频内容中的要点和见解。""",
    tools=[youtube_channel_tool],
    verbose=True,
)

# 为智能体创建任务
research_task = Task(
    description="""
    在 YouTube 频道 {youtube_channel_handle} 中搜索有关数据科学项目和教程的信息。
  
    重点关注：
    1. 涵盖的关键数据科学技术
    2. 热门的教程系列
    3. 观看次数最多或推荐的视频
  
    请提供这些要点的全面摘要。
    """,
    expected_output="关于频道上可用数据科学内容的详细摘要。",
    agent=channel_researcher,
)

# 运行任务
crew = Crew(agents=[channel_researcher], tasks=[research_task])
result = crew.kickoff(inputs={"youtube_channel_handle": "@exampleDataScienceChannel"})
```

## 实现细节

`YoutubeChannelSearchTool` 是作为 `RagTool` 的子类实现的，它提供了检索增强生成的基础功能：

```python Code theme={null}
class YoutubeChannelSearchTool(RagTool):
    name: str = "搜索 YouTube 频道内容"
    description: str = "一个用于从 YouTube 频道内容中进行语义搜索查询的工具。"
    args_schema: Type[BaseModel] = YoutubeChannelSearchToolSchema

    def __init__(self, youtube_channel_handle: Optional[str] = None, **kwargs):
        super().__init__(**kwargs)
        if youtube_channel_handle is not None:
            kwargs["data_type"] = DataType.YOUTUBE_CHANNEL
            self.add(youtube_channel_handle)
            self.description = f"一个用于从 {youtube_channel_handle} YouTube 频道内容中进行语义搜索查询的工具。"
            self.args_schema = FixedYoutubeChannelSearchToolSchema
            self._generate_description()

    def add(
        self,
        youtube_channel_handle: str,
        **kwargs: Any,
    ) -> None:
        if not youtube_channel_handle.startswith("@"):
            youtube_channel_handle = f"@{youtube_channel_handle}"
        super().add(youtube_channel_handle, **kwargs)
```

## 结论

`YoutubeChannelSearchTool` 提供了一种强大的方式，使用 RAG 技术从 YouTube 频道内容中搜索和提取信息。通过使智能体能够搜索整个频道的视频，它促进了原本难以执行的信息提取和分析任务。该工具对于从 YouTube 频道进行研究、内容分析和知识提取特别有用。