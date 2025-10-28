# 概述

> 利用 AI 服务，生成图像，处理视觉，并构建智能系统

这些工具与 AI 和机器学习服务集成，通过图像生成、视觉处理和智能代码执行等高级能力来增强您的代理。

## **可用工具**

<CardGroup cols={2}>
  <Card title="DALL-E 工具" icon="image" href="/en/tools/ai-ml/dalletool">
    使用 OpenAI 的 DALL-E 模型生成 AI 图像。
  </Card>

  <Card title="视觉工具" icon="eye" href="/en/tools/ai-ml/visiontool">
    利用计算机视觉能力处理和分析图像。
  </Card>

  <Card title="AI 心智工具" icon="brain" href="/en/tools/ai-ml/aimindtool">
    高级 AI 推理和决策能力。
  </Card>

  <Card title="LlamaIndex 工具" icon="llama" href="/en/tools/ai-ml/llamaindextool">
    使用 LlamaIndex 构建知识库和检索系统。
  </Card>

  <Card title="LangChain 工具" icon="link" href="/en/tools/ai-ml/langchaintool">
    与 LangChain 集成以实现复杂的 AI 工作流。
  </Card>

  <Card title="RAG 工具" icon="database" href="/en/tools/ai-ml/ragtool">
    实现检索增强生成系统。
  </Card>

  <Card title="代码解释器工具" icon="code" href="/en/tools/ai-ml/codeinterpretertool">
    执行 Python 代码并执行数据分析。
  </Card>
</CardGroup>

## **常见用例**

* **内容生成**：创建图像、文本和多媒体内容
* **数据分析**：执行代码并分析复杂数据集
* **知识系统**：构建 RAG 系统和智能数据库
* **计算机视觉**：处理和理解视觉内容
* **AI 安全**：实施内容审核和安全检查

```python  theme={null}
from crewai_tools import DallETool, VisionTool, CodeInterpreterTool

# 创建 AI 工具
image_generator = DallETool()
vision_processor = VisionTool()
code_executor = CodeInterpreterTool()

# 添加到您的代理
agent = Agent(
    role="AI 专家",
    tools=[image_generator, vision_processor, code_executor],
    goal="使用 AI 能力创建和分析内容"
)
```