# Browserbase 网页加载器

> Browserbase 是一个开发者平台，用于可靠地运行、管理和监控无头浏览器。

# `BrowserbaseLoadTool`

## 描述

[Browserbase](https://browserbase.com) 是一个开发者平台，用于可靠地运行、管理和监控无头浏览器。

通过以下功能为您的 AI 数据检索提供支持：

* [无服务器基础设施](https://docs.browserbase.com/under-the-hood) 提供可靠的浏览器，从复杂的用户界面中提取数据
* [隐身模式](https://docs.browserbase.com/features/stealth-mode) 包含指纹识别策略和自动验证码解决功能
* [会话调试器](https://docs.browserbase.com/features/sessions) 使用网络时间线和日志检查您的浏览器会话
* [实时调试](https://docs.browserbase.com/guides/session-debug-connection/browser-remote-control) 快速调试您的自动化流程

## 安装

* 从 [browserbase.com](https://browserbase.com) 获取 API 密钥和项目 ID，并将其设置在环境变量中（`BROWSERBASE_API_KEY`、`BROWSERBASE_PROJECT_ID`）。
* 安装 [Browserbase SDK](http://github.com/browserbase/python-sdk) 以及 `crewai[tools]` 包：

```shell  theme={null}
pip install browserbase 'crewai[tools]'
```

## 示例

如下使用 BrowserbaseLoadTool，允许您的代理加载网站：

```python Code theme={null}
from crewai_tools import BrowserbaseLoadTool

# 使用 Browserbase API 密钥和项目 ID 初始化工具
tool = BrowserbaseLoadTool()
```

## 参数

以下参数可用于自定义 `BrowserbaseLoadTool` 的行为：

| 参数             | 类型     | 描述                                                                 |
| :--------------- | :------- | :------------------------------------------------------------------ |
| **api\_key**     | `string` | *可选*。Browserbase API 密钥。默认为 `BROWSERBASE_API_KEY` 环境变量。 |
| **project\_id**  | `string` | *可选*。Browserbase 项目 ID。默认为 `BROWSERBASE_PROJECT_ID` 环境变量。 |
| **text\_content**| `bool`   | *可选*。仅检索文本内容。默认为 `False`。                           |
| **session\_id**  | `string` | *可选*。提供现有的会话 ID。                                        |
| **proxy**        | `bool`   | *可选*。启用/禁用代理。默认为 `False`。                            |