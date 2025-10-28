# LangChain 工具

> `LangChainTool` 是 LangChain 工具和查询引擎的包装器。

## `LangChainTool`

<Info>
  CrewAI 与 LangChain 全面的[工具列表](https://python.langchain.com/docs/integrations/tools/)无缝集成，所有这些工具都可以与 CrewAI 一起使用。
</Info>

```python Code theme={null}
import os
from dotenv import load_dotenv
from crewai import Agent, Task, Crew
from crewai.tools import BaseTool
from pydantic import Field
from langchain_community.utilities import GoogleSerperAPIWrapper

# 在 .env 文件中设置您的 SERPER_API_KEY 密钥，例如：
# SERPER_API_KEY=<您的 api 密钥>
load_dotenv()

search = GoogleSerperAPIWrapper()

class SearchTool(BaseTool):
    name: str = "搜索"
    description: str = "适用于基于搜索的查询。使用此工具来查找有关市场、公司和趋势的最新信息。"
    search: GoogleSerperAPIWrapper = Field(default_factory=GoogleSerperAPIWrapper)

    def _run(self, query: str) -> str:
        """执行搜索查询并返回结果"""
        try:
            return self.search.run(query)
        except Exception as e:
            return f"执行搜索时出错：{str(e)}"

# 创建智能体
researcher = Agent(
    role='研究分析师',
    goal='收集当前的市场数据和趋势',
    backstory="""您是一位经验丰富的研究分析师，在收集市场情报方面拥有多年经验。
    您以能够找到相关且最新的市场信息并以清晰、可操作的方式呈现而闻名。""",
    tools=[SearchTool()],
    verbose=True
)

# 其余代码...
```

## 结论

工具在扩展 CrewAI 智能体的能力方面起着关键作用，使它们能够承担广泛的任务并有效协作。
在使用 CrewAI 构建解决方案时，请利用定制和现有工具来增强您的智能体并提升 AI 生态系统。考虑使用错误处理、缓存机制
和工具参数的灵活性来优化您的智能体的性能和能力。