# GitHub 搜索

> `GithubSearchTool` 旨在搜索网站并将其转换为干净的 markdown 或结构化数据。

# `GithubSearchTool`

<Note>
  我们仍在努力改进工具，因此未来可能会出现意外行为或变更。
</Note>

## 描述

GithubSearchTool 是一个检索增强生成（RAG）工具，专门设计用于在 GitHub 仓库中进行语义搜索。利用先进的语义搜索功能，它可以筛选代码、拉取请求、问题和仓库，使开发者、研究人员或任何需要从 GitHub 获取精确信息的人都能使用这个必备工具。

## 安装

要使用 GithubSearchTool，首先确保在您的 Python 环境中安装了 crewai_tools 包：

```shell theme={null}
pip install 'crewai[tools]'
```

此命令安装运行 GithubSearchTool 所需的软件包以及 crewai_tools 包中包含的任何其他工具。

在 [https://github.com/settings/tokens](https://github.com/settings/tokens)（开发者设置 → 细粒度令牌或经典令牌）获取 GitHub 个人访问令牌。

## 示例

以下是如何使用 GithubSearchTool 在 GitHub 仓库中执行语义搜索的方法：

```python Code theme={null}
from crewai_tools import GithubSearchTool

# 初始化工具，用于在特定 GitHub 仓库中进行语义搜索
tool = GithubSearchTool(
	github_repo='https://github.com/example/repo',
	gh_token='your_github_personal_access_token',
	content_types=['code', 'issue'] # 选项：code, repo, pr, issue
)

# 或者

# 初始化工具，用于在特定 GitHub 仓库中进行语义搜索，以便代理在执行过程中可以搜索任何仓库
tool = GithubSearchTool(
	gh_token='your_github_personal_access_token',
	content_types=['code', 'issue'] # 选项：code, repo, pr, issue
)
```

## 参数

* `github_repo`：将进行搜索的 GitHub 仓库的 URL。这是一个必填字段，指定搜索的目标仓库。
* `gh_token`：您的 GitHub 个人访问令牌（PAT），用于身份验证。您可以在 GitHub 账户设置的开发者设置 > 个人访问令牌中创建一个。
* `content_types`：指定搜索中包含的内容类型。您必须从以下选项中提供一个内容类型列表：`code` 用于在代码中搜索，`repo` 用于在仓库的一般信息中搜索，`pr` 用于在拉取请求中搜索，`issue` 用于在问题中搜索。此字段是必填的，允许将搜索调整为 GitHub 仓库中的特定内容类型。

## 自定义模型和嵌入

默认情况下，该工具使用 OpenAI 进行嵌入和摘要。要自定义模型，您可以使用配置字典，如下所示：

```python Code theme={null}
tool = GithubSearchTool(
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