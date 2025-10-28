# 概述

> 执行网页搜索、查找库和跨互联网研究信息

这些工具使您的代理能够搜索网页、研究主题并在包括搜索引擎、GitHub和YouTube在内的各种平台上查找信息。

## **可用工具**

<CardGroup cols={2}>
  <Card title="Serper开发工具" icon="google" href="/en/tools/search-research/serperdevtool">
    集成Google搜索API，提供全面的网页搜索功能。
  </Card>

  <Card title="Brave搜索工具" icon="shield" href="/en/tools/search-research/bravesearchtool">
    使用Brave独立搜索索引进行注重隐私的搜索。
  </Card>

  <Card title="Exa搜索工具" icon="magnifying-glass" href="/en/tools/search-research/exasearchtool">
    AI驱动的搜索，用于查找特定和相关内容。
  </Card>

  <Card title="LinkUp搜索工具" icon="link" href="/en/tools/search-research/linkupsearchtool">
    实时网页搜索，具有新鲜内容索引。
  </Card>

  <Card title="GitHub搜索工具" icon="github" href="/en/tools/search-research/githubsearchtool">
    搜索GitHub仓库、代码、问题和文档。
  </Card>

  <Card title="网站搜索工具" icon="globe" href="/en/tools/search-research/websitesearchtool">
    在特定网站和域名内进行搜索。
  </Card>

  <Card title="代码文档搜索工具" icon="code" href="/en/tools/search-research/codedocssearchtool">
    搜索代码文档和技术资源。
  </Card>

  <Card title="YouTube频道搜索" icon="youtube" href="/en/tools/search-research/youtubechannelsearchtool">
    搜索YouTube频道以查找特定内容和创作者。
  </Card>

  <Card title="YouTube视频搜索" icon="play" href="/en/tools/search-research/youtubevideosearchtool">
    按主题、关键词或标准查找和分析YouTube视频。
  </Card>

  <Card title="Tavily搜索工具" icon="magnifying-glass" href="/en/tools/search-research/tavilysearchtool">
    使用Tavily的AI驱动搜索API进行全面的网页搜索。
  </Card>

  <Card title="Tavily提取工具" icon="file-text" href="/en/tools/search-research/tavilyextractortool">
    使用Tavily API从网页中提取结构化内容。
  </Card>

  <Card title="Arxiv论文工具" icon="box-archive" href="/en/tools/search-research/arxivpapertool">
    搜索arXiv并可选择下载PDF。
  </Card>

  <Card title="SerpApi谷歌搜索" icon="search" href="/en/tools/search-research/serpapi-googlesearchtool">
    通过SerpApi进行谷歌搜索，提供结构化结果。
  </Card>

  <Card title="SerpApi谷歌购物" icon="cart-shopping" href="/en/tools/search-research/serpapi-googleshoppingtool">
    通过SerpApi进行谷歌购物查询。
  </Card>
</CardGroup>

## **常见用例**

* **市场研究**：搜索行业趋势和竞争对手分析
* **内容发现**：查找相关文章、视频和资源
* **代码研究**：搜索仓库和文档以寻找解决方案
* **潜在客户开发**：研究公司和个人
* **学术研究**：查找学术论文和技术论文

```python  theme={null}
from crewai_tools import SerperDevTool, GitHubSearchTool, YoutubeVideoSearchTool, TavilySearchTool, TavilyExtractorTool

# 创建研究工具
web_search = SerperDevTool()
code_search = GitHubSearchTool()
video_research = YoutubeVideoSearchTool()
tavily_search = TavilySearchTool()
content_extractor = TavilyExtractorTool()

# 添加到您的代理
agent = Agent(
    role="研究分析师",
    tools=[web_search, code_search, video_research, tavily_search, content_extractor],
    goal="收集任何主题的全面信息"
)
```