# EXA 搜索网页加载器

> `EXASearchTool` 旨在对互联网上文本内容中的指定查询进行语义搜索。

# `EXASearchTool`

## 描述

EXASearchTool 旨在对互联网上文本内容中的指定查询进行语义搜索。
它利用 [exa.ai](https://exa.ai/) API 来获取并显示基于用户提供查询的最相关搜索结果。

## 安装

要将此工具集成到您的项目中，请按照以下安装说明进行操作：

```shell theme={null}
pip install 'crewai[tools]'
```

## 示例

以下示例演示了如何初始化工具并使用给定查询执行搜索：

```python Code theme={null}
from crewai_tools import EXASearchTool

# 初始化工具以实现互联网搜索功能
tool = EXASearchTool()
```

## 入门步骤

要有效使用 EXASearchTool，请按照以下步骤操作：

<Steps>
  <Step title="包安装">
    确认在您的 Python 环境中已安装 `crewai[tools]` 包。
  </Step>

  <Step title="API 密钥获取">
    通过在 [exa.ai](https://exa.ai/) 注册免费账户来获取 [exa.ai](https://exa.ai/) API 密钥。
  </Step>

  <Step title="环境配置">
    将您获取的 API 密钥存储在名为 `EXA_API_KEY` 的环境变量中，以便工具使用。
  </Step>
</Steps>

## 结论

通过将 `EXASearchTool` 集成到 Python 项目中，用户可以直接从其应用程序中在互联网上进行实时、相关的搜索。
通过遵循提供的设置和使用指南，将此工具集成到项目中变得简单明了。