# 可流式 HTTP 传输

> 了解如何使用灵活的可流式 HTTP 传输将 CrewAI 连接到远程 MCP 服务器。

## 概述

可流式 HTTP 传输提供了一种灵活的方式来连接远程 MCP 服务器。它通常基于 HTTP 构建，可以支持多种通信模式，包括请求-响应和流式传输，有时在更广泛的 HTTP 交互中利用服务器发送事件（SSE）来实现服务器到客户端的流。

## 核心概念

* **远程服务器**：专为远程托管的 MCP 服务器设计。
* **灵活性**：可以支持比纯 SSE 更复杂的交互模式，如果服务器实现，可能包括双向通信。
* **`MCPServerAdapter` 配置**：您需要提供 MCP 通信的服务器基础 URL，并指定 `"streamable-http"` 作为传输类型。

## 通过可流式 HTTP 连接

您有两种主要方法来管理与可流式 HTTP MCP 服务器的连接生命周期：

### 1. 完全托管连接（推荐）

推荐的方法是使用 Python 上下文管理器（`with` 语句），它会自动处理连接的设置和拆除。

```python  theme={null}
from crewai import Agent, Task, Crew, Process
from crewai_tools import MCPServerAdapter

server_params = {
    "url": "http://localhost:8001/mcp", # 替换为您的实际可流式 HTTP 服务器 URL
    "transport": "streamable-http"
}

try:
    with MCPServerAdapter(server_params) as tools:
        print(f"来自可流式 HTTP MCP 服务器的可用工具：{[tool.name for tool in tools]}")

        http_agent = Agent(
            role="HTTP 服务集成器",
            goal="通过可流式 HTTP 利用远程 MCP 服务器的工具。",
            backstory="一个擅长与复杂 Web 服务交互的 AI 代理。",
            tools=tools,
            verbose=True,
        )

        http_task = Task(
            description="使用可流式 HTTP 服务器的工具执行复杂数据查询。",
            expected_output="复杂数据查询的结果。",
            agent=http_agent,
        )

        http_crew = Crew(
            agents=[http_agent],
            tasks=[http_task],
            verbose=True,
            process=Process.sequential
        )
      
        result = http_crew.kickoff() 
        print("\n团队任务结果（可流式 HTTP - 托管）：\n", result)

except Exception as e:
    print(f"连接或使用可流式 HTTP MCP 服务器时出错（托管）：{e}")
    print("确保可流式 HTTP MCP 服务器正在运行且可在指定 URL 访问。")
```

**注意**：将 `"http://localhost:8001/mcp"` 替换为您的实际可流式 HTTP MCP 服务器的 URL。

### 2. 手动连接生命周期

对于需要更明确控制的场景，您可以手动管理 `MCPServerAdapter` 连接。

<Info>
  完成后**必须**调用 `mcp_server_adapter.stop()` 来关闭连接并释放资源。`try...finally` 块是确保这一点的最安全方法。
</Info>

```python  theme={null}
from crewai import Agent, Task, Crew, Process
from crewai_tools import MCPServerAdapter

server_params = {
    "url": "http://localhost:8001/mcp", # 替换为您的实际可流式 HTTP 服务器 URL
    "transport": "streamable-http"
}

mcp_server_adapter = None 
try:
    mcp_server_adapter = MCPServerAdapter(server_params)
    mcp_server_adapter.start()
    tools = mcp_server_adapter.tools
    print(f"可用工具（手动可流式 HTTP）：{[tool.name for tool in tools]}")

    manual_http_agent = Agent(
        role="高级 Web 服务用户",
        goal="使用手动管理的可流式 HTTP 连接与 MCP 服务器交互。",
        backstory="一位专注于调整基于 HTTP 的服务集成的 AI 专家。",
        tools=tools,
        verbose=True
    )
  
    data_processing_task = Task(
        description="通过可流式 HTTP 提交数据进行处理并检索结果。",
        expected_output="已处理的数据或确认信息。",
        agent=manual_http_agent
    )
  
    data_crew = Crew(
        agents=[manual_http_agent],
        tasks=[data_processing_task],
        verbose=True,
        process=Process.sequential
    )
  
    result = data_crew.kickoff()
    print("\n团队任务结果（可流式 HTTP - 手动）：\n", result)

except Exception as e:
    print(f"手动可流式 HTTP MCP 集成期间发生错误：{e}")
    print("确保可流式 HTTP MCP 服务器正在运行且可访问。")
finally:
    if mcp_server_adapter and mcp_server_adapter.is_connected:
        print("正在停止可流式 HTTP MCP 服务器连接（手动）...")
        mcp_server_adapter.stop()  # **关键：确保调用 stop 方法**
    elif mcp_server_adapter:
        print("可流式 HTTP MCP 服务器适配器未连接。无需停止或启动失败。")
```

## 安全考虑

使用可流式 HTTP 传输时，通用的 Web 安全最佳实践至关重要：

* **使用 HTTPS**：始终优先为您的 MCP 服务器 URL 使用 HTTPS（HTTP 安全）以加密传输中的数据。
* **身份验证**：如果您的 MCP 服务器暴露敏感工具或数据，请实施强大的身份验证机制。
* **输入验证**：确保您的 MCP 服务器验证所有传入请求和参数。

有关保护 MCP 集成的全面指南，请参阅我们的[安全考虑](./security.mdx)页面和官方[MCP 传输安全文档](https://modelcontextprotocol.io/docs/concepts/transports#security-considerations)。