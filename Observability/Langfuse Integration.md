# Langfuse 集成

> 了解如何通过使用 OpenLit 的 OpenTelemetry 将 Langfuse 与 CrewAI 集成

# 将 Langfuse 与 CrewAI 集成

本笔记本演示了如何通过 **OpenLit** SDK 使用 OpenTelemetry 将 **Langfuse** 与 **CrewAI** 集成。在本笔记本结束时，您将能够使用 Langfuse 跟踪您的 CrewAI 应用程序，以提高可观察性和调试能力。

> **什么是 Langfuse？** [Langfuse](https://langfuse.com) 是一个开源的 LLM 工程平台。它为 LLM 应用程序提供跟踪和监控功能，帮助开发者调试、分析和优化他们的 AI 系统。Langfuse 通过原生集成、OpenTelemetry 以及 API/SDK 与各种工具和框架集成。

[![Langfuse 概览视频](https://github.com/user-attachments/assets/3926b288-ff61-4b95-8aa1-45d041c70866)](https://langfuse.com/watch-demo)

## 入门指南

我们将通过一个简单的示例来介绍如何使用 CrewAI，并通过 OpenLit 使用 OpenTelemetry 将其与 Langfuse 集成。

### 第 1 步：安装依赖项

```python  theme={null}
%pip install langfuse openlit crewai crewai_tools
```

### 第 2 步：设置环境变量

设置您的 Langfuse API 密钥并配置 OpenTelemetry 导出设置，以将跟踪数据发送到 Langfuse。请参考 [Langfuse OpenTelemetry 文档](https://langfuse.com/docs/opentelemetry/get-started) 了解有关 Langfuse OpenTelemetry 端点 `/api/public/otel` 和身份验证的更多信息。

```python  theme={null}
import os
 
# 从项目设置页面获取您项目的密钥：https://cloud.langfuse.com
os.environ["LANGFUSE_PUBLIC_KEY"] = "pk-lf-..." 
os.environ["LANGFUSE_SECRET_KEY"] = "sk-lf-..."
os.environ["LANGFUSE_HOST"] = "https://cloud.langfuse.com" # 🇪🇺 欧盟区域
# os.environ["LANGFUSE_HOST"] = "https://us.cloud.langfuse.com" # 🇺🇸 美国区域
 
 
# 您的 OpenAI 密钥
os.environ["OPENAI_API_KEY"] = "sk-proj-..."
```

设置好环境变量后，我们现在可以初始化 Langfuse 客户端。get_client() 使用环境变量中提供的凭据初始化 Langfuse 客户端。

```python  theme={null}
from langfuse import get_client
 
langfuse = get_client()
 
# 验证连接
if langfuse.auth_check():
    print("Langfuse 客户端已通过身份验证并准备就绪！")
else:
    print("身份验证失败。请检查您的凭据和主机。")
```

### 第 3 步：初始化 OpenLit

初始化 OpenLit OpenTelemetry 检测 SDK 以开始捕获 OpenTelemetry 跟踪数据。

```python  theme={null}
import openlit

openlit.init()
```

### 第 4 步：创建一个简单的 CrewAI 应用程序

我们将创建一个简单的 CrewAI 应用程序，其中多个代理协作回答用户的问题。

```python  theme={null}
from crewai import Agent, Task, Crew

from crewai_tools import (
    WebsiteSearchTool
)

web_rag_tool = WebsiteSearchTool()

writer = Agent(
        role="Writer",
        goal="You make math engaging and understandable for young children through poetry",
        backstory="You're an expert in writing haikus but you know nothing of math.",
        tools=[web_rag_tool],
    )

task = Task(description=("What is {multiplication}?"),
            expected_output=("Compose a haiku that includes the answer."),
            agent=writer)

crew = Crew(
  agents=[writer],
  tasks=[task],
  share_crew=False
)
```

### 第 5 步：在 Langfuse 中查看跟踪数据

运行代理后，您可以在 [Langfuse](https://cloud.langfuse.com) 中查看由您的 CrewAI 应用程序生成的跟踪数据。您应该能看到 LLM 交互的详细步骤，这可以帮助您调试和优化您的 AI 代理。

![Langfuse 中的 CrewAI 示例跟踪](https://langfuse.com/images/cookbook/integration_crewai/crewai-example-trace.png)

*[Langfuse 中的公共示例跟踪](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/e2cf380ffc8d47d28da98f136140642b?timestamp=2025-02-05T15%3A12%3A02.717Z\&observation=3b32338ee6a5d9af)*

## 参考资料

* [Langfuse OpenTelemetry 文档](https://langfuse.com/docs/opentelemetry/get-started)