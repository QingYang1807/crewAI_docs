# 标准输入/输出传输

> 了解如何使用标准输入/输出(Stdio)传输机制将CrewAI连接到本地MCP服务器。

## 概述

标准输入/输出(Stdio)传输机制旨在将`MCPServerAdapter`连接到通过其标准输入和输出流进行通信的本地MCP服务器。当MCP服务器是在与CrewAI应用程序同一台机器上运行的脚本或可执行文件时，通常会使用这种方式。

## 核心概念

* **本地执行**：Stdio传输管理MCP服务器的本地运行进程。
* **`StdioServerParameters`**：这是`mcp`库中的一个类，用于配置启动Stdio服务器的命令、参数和环境变量。

## 通过Stdio连接

您可以使用两种主要方法来管理连接生命周期，从而连接到基于Stdio的MCP服务器：

### 1. 完全托管连接（推荐）

使用Python上下文管理器（`with`语句）是推荐的方法。它会自动处理启动MCP服务器进程，并在退出上下文时停止该进程。

```python  theme={null}
from crewai import Agent, Task, Crew, Process
from crewai_tools import MCPServerAdapter
from mcp import StdioServerParameters
import os

# 创建StdioServerParameters对象
server_params=StdioServerParameters(
    command="python3", 
    args=["servers/your_stdio_server.py"],
    env={"UV_PYTHON": "3.12", **os.environ},
)

with MCPServerAdapter(server_params) as tools:
    print(f"来自Stdio MCP服务器的可用工具：{[tool.name for tool in tools]}")

    # 示例：在CrewAI Agent中使用来自Stdio MCP服务器的工具
    research_agent = Agent(
        role="本地数据处理器",
        goal="使用基于本地Stdio的工具处理数据。",
        backstory="一个通过MCP利用本地脚本执行专门任务的AI。",
        tools=tools,
        reasoning=True,
        verbose=True,
    )
  
    processing_task = Task(
        description="处理输入数据文件'data.txt'并总结其内容。",
        expected_output="处理后的数据摘要。",
        agent=research_agent,
        markdown=True
    )
  
    data_crew = Crew(
        agents=[research_agent],
        tasks=[processing_task],
        verbose=True,
        process=Process.sequential 
    )
 
    result = data_crew.kickoff()
    print("\nCrew任务结果(Stdio - 托管)：\n", result)

```

### 2. 手动连接生命周期

如果您需要对Stdio MCP服务器进程何时启动和停止进行更精细的控制，可以手动管理`MCPServerAdapter`的生命周期。

<Info>
  您**必须**调用`mcp_server_adapter.stop()`来确保服务器进程被终止并释放资源。强烈建议使用`try...finally`块。
</Info>

```python  theme={null}
from crewai import Agent, Task, Crew, Process
from crewai_tools import MCPServerAdapter
from mcp import StdioServerParameters
import os

# 创建StdioServerParameters对象
stdio_params=StdioServerParameters(
    command="python3", 
    args=["servers/your_stdio_server.py"],
    env={"UV_PYTHON": "3.12", **os.environ},
)

mcp_server_adapter = MCPServerAdapter(server_params=stdio_params)
try:
    mcp_server_adapter.start()  # 手动启动连接和服务器进程
    tools = mcp_server_adapter.tools
    print(f"可用工具(手动Stdio)：{[tool.name for tool in tools]}")

    # 示例：使用工具与您的Agent、Task、Crew设置
    manual_agent = Agent(
        role="本地任务执行器",
        goal="使用手动管理的Stdio工具执行特定本地任务。",
        backstory="一个擅长通过MCP控制本地进程的AI。",
        tools=tools,
        verbose=True
    )
  
    manual_task = Task(
        description="通过Stdio工具执行'perform_analysis'命令。",
        expected_output="分析结果。",
        agent=manual_agent
    )
  
    manual_crew = Crew(
        agents=[manual_agent],
        tasks=[manual_task],
        verbose=True,
        process=Process.sequential
    )
      
     
    result = manual_crew.kickoff() # 实际输入取决于您的工具
    print("\nCrew任务结果(Stdio - 手动)：\n", result)
          
except Exception as e:
    print(f"手动Stdio MCP集成过程中发生错误：{e}")
finally:
    if mcp_server_adapter and mcp_server_adapter.is_connected: # 在停止前检查是否已连接
        print("正在停止Stdio MCP服务器连接(手动)...")
        mcp_server_adapter.stop()  # **关键：确保调用stop**
    elif mcp_server_adapter: # 如果适配器存在但未连接（例如启动失败）
        print("Stdio MCP服务器适配器未连接。无需停止或启动失败。")

```

请记得将占位符路径和命令替换为您实际的Stdio服务器详细信息。`StdioServerParameters`中的`env`参数可用于为服务器进程设置环境变量，这对于配置其行为或提供必要的路径（如`PYTHONPATH`）很有用。