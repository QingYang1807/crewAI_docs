# OCR 工具

> `OCRTool` 使用具有视觉功能的大型语言模型（LLM）从本地图像或图像 URL 中提取文本。

# `OCRTool`

## 描述

从图像（本地路径或 URL）中提取文本。通过 CrewAI 的 LLM 接口使用具有视觉功能的大型语言模型。

## 安装

除了 `crewai-tools` 外，无需额外安装。确保所选的 LLM 支持视觉功能。

## 参数

### 运行参数

* `image_path_url`（字符串，必需）：本地图像路径或 HTTP(S) URL。

## 示例

### 直接使用

```python Code theme={null}
from crewai_tools import OCRTool

print(OCRTool().run(image_path_url="/tmp/receipt.png"))
```

### 与代理一起使用

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import OCRTool

ocr = OCRTool()

agent = Agent(
    role="OCR", 
    goal="Extract text", 
    tools=[ocr],
)

task = Task(
    description="Extract text from https://example.com/invoice.jpg", 
    expected_output="All detected text in plain text",
    agent=agent,
)

crew = Crew(agents=[agent], tasks=[task])
result = crew.kickoff()
```

## 注意事项

* 确保所选的 LLM 支持图像输入。
* 对于大尺寸图像，考虑缩小尺寸以减少令牌使用量。
* 如果需要，可以向工具传递特定的 LLM 实例（例如，`LLM(model="gpt-4o")`），遵循 README 指南。

## 示例

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import OCRTool

tool = OCRTool()

agent = Agent(
    role="OCR Specialist",
    goal="Extract text from images",
    backstory="Vision‑enabled analyst",
    tools=[tool],
    verbose=True,
)

task = Task(
    description="Extract text from https://example.com/receipt.png",
    expected_output="All detected text in plain text",
    agent=agent,
)

crew = Crew(agents=[agent], tasks=[task])
result = crew.kickoff()
```