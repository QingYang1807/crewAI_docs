# 连接到多个MCP服务器

> 了解如何在CrewAI中使用MCPServerAdapter同时连接到多个MCP服务器并聚合它们的工具。

## 概述

`crewai-tools`中的`MCPServerAdapter`允许您同时连接到多个MCP服务器。当您的代理需要访问分布在不同的服务或环境中的工具时，这非常有用。该适配器聚合了所有指定服务器的工具，使其可供您的CrewAI代理使用。

## 配置

要连接到多个服务器，您需要向`MCPServerAdapter`提供一个服务器参数字典列表。列表中的每个字典应该定义一个MCP服务器的参数。

列表中每个服务器支持的传输类型包括`stdio`、`sse`和`streamable-http`。

```python  theme={null}
from crewai import Agent, Task, Crew, Process
from crewai_tools import MCPServerAdapter
from mcp import StdioServerParameters # Stdio示例需要

# 定义多个MCP服务器的参数
server_params_list = [
    # 可流式HTTP服务器
    {
        "url": "http://localhost:8001/mcp", 
        "transport": "streamable-http"
    },
    # SSE服务器
    {
        "url": "http://localhost:8000/sse",
        "transport": "sse"
    },
    # StdIO服务器
    StdioServerParameters(
        command="python3",
        args=["servers/your_stdio_server.py"],
        env={"UV_PYTHON": "3.12", **os.environ},
    )
]

try:
    with MCPServerAdapter(server_params_list) as aggregated_tools:
        print(f"可用的聚合工具: {[tool.name for tool in aggregated_tools]}")

        multi_server_agent = Agent(
            role="多功能助手",
            goal="利用来自本地Stdio、远程SSE和远程HTTP MCP服务器的工具。",
            backstory="一个能够利用来自多个来源的多样化工具的AI代理。",
            tools=aggregated_tools, # 所有工具都在这里可用
            verbose=True,
        )

        ... # 您的其他代理、任务和团队代码在这里

except Exception as e:
    print(f"连接或使用多个MCP服务器时出错(已管理): {e}")
    print("确保所有MCP服务器都在运行，并且配置正确可访问。")

```

## 连接管理

当使用上下文管理器(`with`语句)时，`MCPServerAdapter`会处理到所有已配置MCP服务器的连接生命周期(启动和停止)。这简化了资源管理，并确保在退出上下文时所有连接都正确关闭。