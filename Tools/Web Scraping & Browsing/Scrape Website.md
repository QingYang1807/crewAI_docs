# 网站抓取工具

> `ScrapeWebsiteTool` 旨在提取和读取指定网站的内容。

# `ScrapeWebsiteTool`

<Note>
  我们仍在努力改进工具，因此未来可能会有意外行为或变更。
</Note>

## 描述

这是一个设计用于提取和读取指定网站内容的工具。它能够通过发出HTTP请求和解析接收到的HTML内容来处理各种类型的网页。
该工具对于网页抓取任务、数据收集或从网站中提取特定信息特别有用。

## 安装

安装 crewai_tools 包

```shell  theme={null}
pip install 'crewai[tools]'
```

## 示例

```python  theme={null}
from crewai_tools import ScrapeWebsiteTool

# 启用抓取执行过程中发现的任何网站
tool = ScrapeWebsiteTool()

# 使用网站URL初始化工具，
# 这样代理就只能抓取指定网站的内容
tool = ScrapeWebsiteTool(website_url='https://www.example.com')

# 从网站中提取文本
text = tool.run()
print(text)
```

## 参数

| 参数            | 类型      | 描述                                                                                                                                          |
| :-------------- | :-------- | :------------------------------------------------------------------------------------------------------------------------------------------- |
| **website_url** | `string` | **必需** 要读取文件的网站URL。这是该工具的主要输入，指定应该抓取和读取哪个网站的内容。                                                              |