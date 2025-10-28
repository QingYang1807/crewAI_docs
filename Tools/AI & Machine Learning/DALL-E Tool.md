# DALL-E 工具

> `DallETool` 是一个强大的工具，专为根据文本描述生成图像而设计。

# `DallETool`

## 描述

此工具用于为 Agent 提供使用 DALL-E 模型生成图像的能力。它是一个基于变换器的模型，能够根据文本描述生成图像。
该工具允许 Agent 根据用户提供的文本输入生成图像。

## 安装

安装 crewai_tools 包

```shell theme={null}
pip install 'crewai[tools]'
```

## 示例

请记住，使用此工具时，文本必须由 Agent 自己生成。文本必须是对您想要生成的图像的描述。

```python Code theme={null}
from crewai_tools import DallETool

Agent(
    ...
    tools=[DallETool()],
)
```

如果需要，您还可以通过将参数作为参数传递给 `DallETool` 类来调整 DALL-E 模型的参数。例如：

```python Code theme={null}
from crewai_tools import DallETool

dalle_tool = DallETool(model="dall-e-3",
                       size="1024x1024",
                       quality="standard",
                       n=1)

Agent(
    ...
    tools=[dalle_tool]
)
```

这些参数基于 OpenAI API 中的 `client.images.generate` 方法。有关参数的更多信息，
请参考 [OpenAI API 文档](https://platform.openai.com/docs/guides/images/introduction?lang=python)。