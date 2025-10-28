# 视觉工具

> `VisionTool` 旨在从图像中提取文本。

# `VisionTool`

## 描述

该工具用于从图像中提取文本。当传递给代理时，它将从图像中提取文本，然后使用该文本生成响应、报告或任何其他输出。
图像的 URL 或 PATH 应传递给代理。

## 安装

安装 crewai_tools 包

```shell  theme={null}
pip install 'crewai[tools]'
```

## 使用方法

为了使用 VisionTool，应在环境变量 `OPENAI_API_KEY` 中设置 OpenAI API 密钥。

```python Code theme={null}
from crewai_tools import VisionTool

vision_tool = VisionTool()

@agent
def researcher(self) -> Agent:
    '''
    该代理使用 VisionTool 从图像中提取文本。
    '''
    return Agent(
        config=self.agents_config["researcher"],
        allow_delegation=False,
        tools=[vision_tool]
    )
```

## 参数

VisionTool 需要以下参数：

| 参数             | 类型     | 描述                                      |
| :--------------- | :------- | :--------------------------------------- |
| **image_path_url** | `字符串` | **必需**。需要从中提取文本的图像文件的路径。 |