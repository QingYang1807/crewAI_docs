# Arxiv论文工具

> `ArxivPaperTool`在arXiv上搜索与查询匹配的论文，并可选择下载PDF文件。

# `ArxivPaperTool`

## 描述

`ArxivPaperTool`查询arXiv API获取学术论文，并返回简洁、可读的结果。它还可以选择将PDF文件下载到本地磁盘。

## 安装

除了`crewai-tools`外，此工具无需特殊安装。

```shell  theme={null}
uv add crewai-tools
```

不需要API密钥。此工具使用公开的arXiv Atom API。

## 入门步骤

1. 初始化工具。
2. 提供一个`search_query`（例如，"transformer neural network"）。
3. 可选择在构造函数中设置`max_results`（1-100）并启用PDF下载。

## 示例

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import ArxivPaperTool

tool = ArxivPaperTool(
    download_pdfs=False,
    save_dir="./arxiv_pdfs",
    use_title_as_filename=True,
)

agent = Agent(
    role="Researcher",
    goal="Find relevant arXiv papers",
    backstory="Expert at literature discovery",
    tools=[tool],
    verbose=True,
)

task = Task(
    description="Search arXiv for 'transformer neural network' and list top 5 results.",
    expected_output="A concise list of 5 relevant papers with titles, links, and summaries.",
    agent=agent,
)

crew = Crew(agents=[agent], tasks=[task])
result = crew.kickoff()
```

### 直接使用（不使用Agent）

```python Code theme={null}
from crewai_tools import ArxivPaperTool

tool = ArxivPaperTool(
    download_pdfs=True, 
    save_dir="./arxiv_pdfs",
)
print(tool.run(search_query="mixture of experts", max_results=3))
```

## 参数

### 初始化参数

* `download_pdfs`（布尔值，默认为`False`）：是否下载PDF文件。
* `save_dir`（字符串，默认为`./arxiv_pdfs`）：保存PDF文件的目录。
* `use_title_as_filename`（布尔值，默认为`False`）：使用论文标题作为文件名。

### 运行参数

* `search_query`（字符串，必需）：arXiv搜索查询。
* `max_results`（整数，默认为`5`，范围1-100）：返回结果数量。

## 输出格式

该工具返回一个人类可读的论文列表，包含：

* 标题
* 链接（摘要页面）
* 摘要/简介（截断的）

当`download_pdfs=True`时，PDF文件会保存到磁盘，并且摘要中会提及已保存的文件。

## 使用说明

* 该工具返回包含关键元数据和链接的格式化文本。
* 当`download_pdfs=True`时，PDF文件将存储在`save_dir`中。

## 故障排除

* 如果遇到网络超时，请重试或减少`max_results`。
* 无效的XML错误表示arXiv响应解析问题；请尝试更简单的查询。
* 保存PDF文件时可能会出现文件系统错误（如权限被拒绝）；请确保`save_dir`是可写的。

## 相关链接

* arXiv API文档：[https://info.arxiv.org/help/api/index.html](https://info.arxiv.org/help/api/index.html)

## 错误处理

* 网络问题、无效XML和操作系统错误都会通过信息性消息进行处理。