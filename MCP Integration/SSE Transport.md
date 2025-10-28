# SSE 传输

> 了解如何使用服务器发送事件（SSE）将 CrewAI 连接到远程 MCP 服务器，实现实时通信。

## 概述

服务器发送事件（SSE）提供了一种标准方式，让 Web 服务器通过单一的长期 HTTP 连接向客户端发送更新。在 MCP 的上下文中，SSE 用于远程服务器向您的 CrewAI 应用程序实时流式传输数据（如工具响应）。

## 关键概念

* **远程服务器**：SSE 适用于远程托管的 MCP 服务器。
* **单向流**：通常，SSE 是从服务器到客户端的单向通信通道。
* **`MCPServerAdapter` 配置**：对于 SSE，您需要提供服务器的 URL 并指定传输类型。

## 通过 SSE 连接

您可以通过两种主要方法连接到基于 SSE 的 MCP 服务器，以管理连接生命周期：

### 1. 完全托管连接（推荐）

使用 Python 上下文管理器（`with` 语句）是推荐的方法。它会自动处理与 SSE MCP 服务器的建立和关闭连接。

```python
from crewai import Agent, Task, Crew, Process
from crewai_tools import MCPServerAdapter

server_params = {
    "url": "http://localhost:8000/sse", # 替换为您实际的 SSE 服务器 URL
    "transport": "sse" 
}

# 使用上下文管理器操作 MCPServerAdapter
try:
    with MCPServerAdapter(server_params) as tools:
        print(f"来自 SSE MCP 服务器的可用工具: {[tool.name for tool in tools]}")

        # 示例：使用来自 SSE MCP 服务器的工具
        sse_agent = Agent(
            role="远程服务用户",
            goal="利用远程 SSE MCP 服务器提供的工具。",
            backstory="通过 SSE 连接到外部服务的 AI 代理。",
            tools=tools,
            reasoning=True,
            verbose=True,
        )

        sse_task = Task(
            description="使用 SSE 工具获取 'AAPL' 的实时股票更新。",
            expected_output="AAPL 的最新股票价格。",
            agent=sse_agent,
            markdown=True
        )

        sse_crew = Crew(
            agents=[sse_agent],
            tasks=[sse_task],
            verbose=True,
            process=Process.sequential
        )
      
        if tools: # 仅在加载工具时启动
            result = sse_crew.kickoff() # 如果工具需要，添加 inputs={'stock_symbol': 'AAPL'}
            print("\n团队任务结果 (SSE - 托管):\n", result)
        else:
            print("跳过团队启动，因为未加载工具（请检查服务器连接）。")

except Exception as e:
    print(f"连接或使用 SSE MCP 服务器时出错 (托管): {e}")
    print("确保 SSE MCP 服务器正在运行且可在指定 URL 访问。")

```

<Note>
  将 `"http://localhost:8000/sse"` 替换为您实际的 SSE MCP 服务器 URL。
</Note>

### 2. 手动连接生命周期

如果您需要更细粒度的控制，可以手动管理 `MCPServerAdapter` 连接生命周期。

<Info>
  您**必须**调用 `mcp_server_adapter.stop()` 以确保连接关闭并释放资源。强烈建议使用 `try...finally` 块。
</Info>

```python
from crewai import Agent, Task, Crew, Process
from crewai_tools import MCPServerAdapter

server_params = {
    "url": "http://localhost:8000/sse", # 替换为您实际的 SSE 服务器 URL
    "transport": "sse"
}

mcp_server_adapter = None 
try:
    mcp_server_adapter = MCPServerAdapter(server_params)
    mcp_server_adapter.start()
    tools = mcp_server_adapter.tools
    print(f"可用工具 (手动 SSE): {[tool.name for tool in tools]}")

    manual_sse_agent = Agent(
        role="远程数据分析师",
        goal="使用手动连接管理分析从远程 SSE MCP 服务器获取的数据。",
        backstory="擅长明确处理 SSE 连接的 AI。",
        tools=tools,
        verbose=True
    )
  
    analysis_task = Task(
        description="从 SSE 服务器获取并分析最新的用户活动趋势。",
        expected_output="用户活动趋势的总结报告。",
        agent=manual_sse_agent
    )
  
    analysis_crew = Crew(
        agents=[manual_sse_agent],
        tasks=[analysis_task],
        verbose=True,
        process=Process.sequential
    )
  
    result = analysis_crew.kickoff()
    print("\n团队任务结果 (SSE - 手动):\n", result)

except Exception as e:
    print(f"手动 SSE MCP 集成期间发生错误: {e}")
    print("确保 SSE MCP 服务器正在运行且可访问。")
finally:
    if mcp_server_adapter and mcp_server_adapter.is_connected:
        print("正在停止 SSE MCP 服务器连接 (手动)...")
        mcp_server_adapter.stop()  # **关键：确保调用 stop**
    elif mcp_server_adapter:
        print("SSE MCP 服务器适配器未连接。无需停止或启动失败。")

```

## SSE 的安全考虑

<Warning>
  **DNS 重绑定攻击**：如果 MCP 服务器未正确保护，SSE 传输可能容易受到 DNS 重绑定攻击。这可能允许恶意网站与本地或基于内网的 MCP 服务器交互。
</Warning>

要减轻此风险：

* MCP 服务器实现应该对传入的 SSE 连接**验证 `Origin` 标头**。
* 当为开发运行本地 SSE MCP 服务器时，**仅绑定到 `localhost` (`127.0.0.1`)**，而不是所有网络接口 (`0.0.0.0`)。
* 如果 SSE 连接暴露敏感工具或数据，请为所有连接实现**适当的身份验证**。

有关安全最佳实践的全面概述，请参阅我们的[安全考虑](./security.mdx)页面和官方的[MCP 传输安全文档](https://modelcontextprotocol.io/docs/concepts/transports#security-considerations)。