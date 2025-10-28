# YouTube 视频 RAG 搜索

> `YoutubeVideoSearchTool` 旨在对 YouTube 视频的内容执行 RAG（检索增强生成）搜索。

# `YoutubeVideoSearchTool`

<Note>
  我们仍在努力改进工具，因此未来可能出现意外行为或变更。
</Note>

## 描述

该工具是 `crewai_tools` 包的一部分，旨在利用检索增强生成（RAG）技术对 YouTube 视频内容执行语义搜索。
它是该包中利用 RAG 技术针对不同数据源的多个“搜索”工具之一。
`YoutubeVideoSearchTool` 提供了灵活的搜索方式；用户可以在未指定视频 URL 的情况下搜索任意 YouTube 视频内容，也可以通过提供特定视频的 URL 来进行精准搜索。

## 安装

要使用 `YoutubeVideoSearchTool`，您必须先安装 `crewai_tools` 包。
该包包含 `YoutubeVideoSearchTool` 以及其他旨在增强您数据处理和分析任务的实用工具。
在终端中执行以下命令来安装该包：

```shell theme={null}
pip install 'crewai[tools]'
```

## 示例

以下示例演示了如何将 `YoutubeVideoSearchTool` 与 CrewAI 智能体结合使用：

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import YoutubeVideoSearchTool

# 初始化工具，用于通用的 YouTube 视频搜索
youtube_search_tool = YoutubeVideoSearchTool()

# 定义一个使用该工具的智能体
video_researcher = Agent(
    role="视频研究员",
    goal="从 YouTube 视频中提取相关信息",
    backstory="一名专门分析视频内容的专家研究员。",
    tools=[youtube_search_tool],
    verbose=True,
)

# 示例任务：在特定视频中搜索信息
research_task = Task(
    description="在位于 {youtube_video_url} 的 YouTube 视频中搜索有关机器学习框架的信息",
    expected_output="视频中提到的关键机器学习框架的摘要。",
    agent=video_researcher,
)

# 创建并运行团队
crew = Crew(agents=[video_researcher], tasks=[research_task])
result = crew.kickoff(inputs={"youtube_video_url": "https://youtube.com/watch?v=example"})
```

您也可以在初始化工具时提供特定的 YouTube 视频 URL：

```python Code theme={null}
# 使用特定的 YouTube 视频 URL 初始化工具
youtube_search_tool = YoutubeVideoSearchTool(
    youtube_video_url='https://youtube.com/watch?v=example'
)

# 定义一个使用该工具的智能体
video_researcher = Agent(
    role="视频研究员",
    goal="从特定的 YouTube 视频中提取相关信息",
    backstory="一名专门分析视频内容的专家研究员。",
    tools=[youtube_search_tool],
    verbose=True,
)
```

## 参数

`YoutubeVideoSearchTool` 接受以下参数：

* **youtube\_video\_url**：可选。要搜索的 YouTube 视频的 URL。如果在初始化时提供，智能体在使用该工具时将无需再指定。
* **config**：可选。底层 RAG 系统的配置，包括 LLM 和嵌入器设置。
* **summarize**：可选。是否对检索到的内容进行摘要。默认为 `False`。

当智能体使用该工具时，需要提供：

* **search\_query**：必需。用于在视频内容中查找相关信息的搜索查询。
* **youtube\_video\_url**：仅在初始化时未提供的情况下为必需。要搜索的 YouTube 视频的 URL。

## 自定义模型和嵌入

默认情况下，该工具对嵌入和摘要均使用 OpenAI。要自定义模型，您可以使用如下配置字典：

```python Code theme={null}
youtube_search_tool = YoutubeVideoSearchTool(
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

以下是一个更详细的示例，展示了如何将 `YoutubeVideoSearchTool` 与 CrewAI 智能体集成：

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import YoutubeVideoSearchTool

# 初始化工具
youtube_search_tool = YoutubeVideoSearchTool()

# 定义一个使用该工具的智能体
video_researcher = Agent(
    role="视频研究员",
    goal="从 YouTube 视频中提取和分析信息",
    backstory="""你是一位专业的视频研究员，专门从 YouTube 视频中提取
    和分析信息。你眼光敏锐，能够快速从视频内容中识别出关键要点和洞察。""",
    tools=[youtube_search_tool],
    verbose=True,
)

# 为智能体创建任务
research_task = Task(
    description="""
    在位于 {youtube_video_url} 的 YouTube 视频中搜索有关人工智能最新进展的信息。
  
    重点关注：
    1. 提到的主要 AI 技术
    2. 讨论的实际应用
    3. 讲者做出的未来预测
  
    提供关于这些要点的全面摘要。
    """,
    expected_output="一份关于视频中 AI 进展、应用和未来预测的详细摘要。",
    agent=video_researcher,
)

# 运行任务
crew = Crew(agents=[video_researcher], tasks=[research_task])
result = crew.kickoff(inputs={"youtube_video_url": "https://youtube.com/watch?v=example"})
```

## 实现细节

`YoutubeVideoSearchTool` 作为 `RagTool` 的子类实现，后者提供了检索增强生成的基础功能：

```python Code theme={null}
class YoutubeVideoSearchTool(RagTool):
    name: str = "Search a Youtube Video content"
    description: str = "A tool that can be used to semantic search a query from a Youtube Video content."
    args_schema: Type[BaseModel] = YoutubeVideoSearchToolSchema

    def __init__(self, youtube_video_url: Optional[str] = None, **kwargs):
        super().__init__(**kwargs)
        if youtube_video_url is not None:
            kwargs["data_type"] = DataType.YOUTUBE_VIDEO
            self.add(youtube_video_url)
            self.description = f"A tool that can be used to semantic search a query the {youtube_video_url} Youtube Video content."
            self.args_schema = FixedYoutubeVideoSearchToolSchema
            self._generate_description()
```

## 结论

`YoutubeVideoSearchTool` 提供了一种强大的方式，使用 RAG 技术从 YouTube 视频内容中搜索和提取信息。通过使智能体能够在视频内容内部进行搜索，它促进了原本难以执行的信息提取和分析任务。该工具对于研究、内容分析和从视频源中提取知识特别有用。