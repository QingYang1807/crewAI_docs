# Spider 爬虫工具

> `SpiderTool` 旨在使用 Spider 提取和读取指定网站的内容。

# `SpiderTool`

## 描述

[Spider](https://spider.cloud/?ref=crewai) 是[最快](https://github.com/spider-rs/spider/blob/main/benches/BENCHMARKS.md#benchmark-results)的开源爬虫和抓取工具，可返回适合 LLM 处理的数据。
它可以将任何网站转换为纯 HTML、markdown、元数据或文本，同时支持使用 AI 进行自定义操作的爬取。

## 安装

要使用 `SpiderTool`，您需要下载 [Spider SDK](https://pypi.org/project/spider-client/) 和 `crewai[tools]` SDK：

```shell theme={null}
pip install spider-client 'crewai[tools]'
```

## 示例

此示例向您展示如何使用 `SpiderTool` 使您的代理能够抓取和爬取网站。
从 Spider API 返回的数据已经适合 LLM 处理，因此无需进行任何清理。

```python Code theme={null}
from crewai_tools import SpiderTool

def main():
    spider_tool = SpiderTool()

    searcher = Agent(
        role="网络研究专家",
        goal="从特定 URL 中查找相关信息",
        backstory="一位非常擅长使用网络的专业网络研究员",
        tools=[spider_tool],
        verbose=True,
    )

    return_metadata = Task(
        description="抓取 https://spider.cloud，限制为 1 页并启用元数据",
        expected_output="spider.cloud 的元数据和 10 字摘要",
        agent=searcher
    )

    crew = Crew(
        agents=[searcher],
        tasks=[
            return_metadata,
        ],
        verbose=2
    )

    crew.kickoff()

if __name__ == "__main__":
    main()
```

## 参数

| 参数                    | 类型     | 描述                                                                                                                               |
| :---------------------- | :------- | :-------------------------------------------------------------------------------------------------------------------------------- |
| **api\_key**            | `string` | 指定 Spider API 密钥。如果未指定，将在环境变量中查找 `SPIDER_API_KEY`。                                                            |
| **params**              | `object` | 请求的可选参数。默认为 `{"return_format": "markdown"}` 以优化 LLM 的内容。                                                          |
| **request**             | `string` | 要执行的请求类型（`http`、`chrome`、`smart`）。`smart` 默认使用 HTTP，在需要时切换到 JavaScript 渲染。                             |
| **limit**               | `int`    | 每个网站爬取的最大页数。设置为 `0` 或省略表示无限制。                                                                             |
| **depth**               | `int`    | 最大爬取深度。设置为 `0` 表示无限制。                                                                                             |
| **cache**               | `bool`   | 启用 HTTP 缓存以加快重复运行速度。默认为 `true`。                                                                                |
| **budget**              | `object` | 为爬取页面设置基于路径的限制，例如 `{"*":1}` 仅限根页面。                                                                         |
| **locale**              | `string` | 请求的区域设置，例如 `en-US`。                                                                                                    |
| **cookies**             | `string` | 请求的 HTTP cookies。                                                                                                             |
| **stealth**             | `bool`   | 为 Chrome 请求启用隐身模式以避免检测。默认为 `true`。                                                                            |
| **headers**             | `object` | HTTP 标头作为所有请求的键值对映射。                                                                                               |
| **metadata**            | `bool`   | 存储有关页面和内容的元数据，有助于 AI 互操作性。默认为 `false`。                                                                 |
| **viewport**            | `object` | 设置 Chrome 视口尺寸。默认为 `800x600`。                                                                                         |
| **encoding**            | `string` | 指定编码类型，例如 `UTF-8`、`SHIFT_JIS`。                                                                                        |
| **subdomains**          | `bool`   | 在爬取中包含子域名。默认为 `false`。                                                                                              |
| **user\_agent**         | `string` | 自定义 HTTP 用户代理。默认为随机代理。                                                                                            |
| **store\_data**         | `bool`   | 启用请求的数据存储。设置时会覆盖 `storageless`。默认为 `false`。                                                                  |
| **gpt\_config**         | `object` | 允许 AI 生成爬取操作，通过 `"prompt"` 的数组进行可选的链接步骤。                                                                  |
| **fingerprint**         | `bool`   | 为 Chrome 启用高级指纹识别。                                                                                                      |
| **storageless**         | `bool`   | 防止所有数据存储，包括 AI 嵌入。默认为 `false`。                                                                                 |
| **readability**         | `bool`   | 通过 [Mozilla 的可读性算法](https://github.com/mozilla/readability)预处理内容以便阅读。改善 LLM 的内容质量。                    |
| **return\_format**      | `string` | 返回数据的格式：`markdown`、`raw`、`text`、`html2text`。使用 `raw` 获取默认页面格式。                                             |
| **proxy\_enabled**      | `bool`   | 启用高性能代理以避免网络级别的阻止。                                                                                             |
| **query\_selector**     | `string` | 用于从标记中提取内容的 CSS 查询选择器。                                                                                           |
| **full\_resources**     | `bool`   | 下载链接到网站的所有资源。                                                                                                       |
| **request\_timeout**    | `int`    | 请求的超时时间（5-60秒）。默认为 `30`。                                                                                          |
| **run\_in\_background** | `bool`   | 在后台运行请求，对数据存储和触发仪表板爬取有用。如果设置了 `storageless` 则无效。                                                  |