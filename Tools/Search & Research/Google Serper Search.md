# Google Serper 搜索

> `SerperDevTool` 旨在搜索互联网并返回最相关的结果。

# `SerperDevTool`

## 描述

该工具旨在根据文本内容执行指定查询的语义搜索，覆盖整个互联网。它利用 [serper.dev](https://serper.dev) API 获取并显示基于用户提供的查询的最相关搜索结果。

## 安装

为有效使用 `SerperDevTool`，请按照以下步骤操作：

1. **软件包安装**：确认在您的 Python 环境中已安装 `crewai[tools]` 软件包。
2. **API 密钥获取**：在 [https://serper.dev/](https://serper.dev/) 获取 `serper.dev` API 密钥（提供免费版本）。
3. **环境配置**：将获取的 API 密钥存储在名为 `SERPER_API_KEY` 的环境变量中，以便工具使用。

要将此工具集成到您的项目中，请遵循以下安装说明：

```shell  theme={null}
pip install 'crewai[tools]'
```

## 示例

以下示例演示了如何初始化工具并执行给定查询的搜索：

```python Code theme={null}
from crewai_tools import SerperDevTool

# 初始化工具以实现互联网搜索功能
tool = SerperDevTool()
```

## 参数

`SerperDevTool` 提供了多个将传递给 API 的参数：

* **search_url**：搜索 API 的 URL 端点。（默认为 `https://google.serper.dev/search`）

* **country**：可选。指定搜索结果的国家。

* **location**：可选。指定搜索结果的位置。

* **locale**：可选。指定搜索结果的语言环境。

* **n_results**：返回的搜索结果数量。默认为 `10`。

`country`、`location`、`locale` 和 `search_url` 的值可以在 [Serper Playground](https://serper.dev/playground) 上找到。

## 带参数的示例

以下是演示如何使用带有附加参数的工具的示例：

```python Code theme={null}
from crewai_tools import SerperDevTool

tool = SerperDevTool(
    search_url="https://google.serper.dev/scholar",
    n_results=2,
)

print(tool.run(search_query="ChatGPT"))

# 使用工具：搜索互联网

# 搜索结果：标题：ChatGPT在公共卫生中的作用
# 链接：https://link.springer.com/article/10.1007/s10439-023-03172-7
# 摘要：……ChatGPT在公共卫生中的应用。在本概述中，我们将探讨ChatGPT的潜在用途
# ---
# 标题：ChatGPT在全球变暖中的潜在应用
# 链接：https://link.springer.com/article/10.1007/s10439-023-03171-8
# 摘要：……如ChatGPT，在推动我们对气候的理解方面可能发挥关键作用
# ---

```

```python Code theme={null}
from crewai_tools import SerperDevTool

tool = SerperDevTool(
    country="fr",
    locale="fr",
    location="Paris, Paris, Ile-de-France, France",
    n_results=2,
)

print(tool.run(search_query="Jeux Olympiques"))

# 使用工具：搜索互联网

# 搜索结果：标题：2024年巴黎奥运会 - 新闻、日程、成绩
# 链接：https://olympics.com/fr/paris-2024
# 摘要：2024年巴黎奥运会有哪些运动项目？· 田径 · 赛艇 · 羽毛球 · 篮球 · 3x3篮球 · 拳击 · 霹雳舞 · 皮划艇...
# ---
# 标题：巴黎2024官方票务 - 奥运会和残奥会
# 链接：https://tickets.paris2024.org/
# 摘要：仅在巴黎2024官方票务网站上购买您的门票，参与世界最大的体育赛事。
# ---
```

## 结论

通过将 `SerperDevTool` 集成到 Python 项目中，用户能够直接从其应用程序在互联网上进行实时、相关的搜索。更新的参数允许更加定制化和本地化的搜索结果。遵循提供的设置和使用指南，将该工具集成到项目中变得简单直接。