# 在CrewAI中将MCP服务器作为工具使用

> 了解如何使用`crewai-tools`库在CrewAI智能体中集成MCP服务器作为工具。

## 概述

[模型上下文协议](https://modelcontextprotocol.io/introduction)(MCP)提供了一种标准化方式，让AI智能体通过称为MCP服务器的外部服务与大型语言模型(LLM)进行上下文通信。

CrewAI提供了**两种方法**进行MCP集成：

### 简单DSL集成\*\*（推荐）

直接在智能体上使用`mcps`字段进行无缝MCP工具集成：

```python  theme={null}
from crewai import Agent

agent = Agent(
    role="研究分析师",
    goal="研究和分析信息",
    backstory="具有访问外部工具权限的专家研究员",
    mcps=[
        "https://mcp.exa.ai/mcp?api_key=your_key",           # 外部MCP服务器
        "https://api.weather.com/mcp#get_forecast",          # 服务器的特定工具
        "crewai-amp:financial-data",                         # CrewAI AMP市场
        "crewai-amp:research-tools#pubmed_search"            # 特定AMP工具
    ]
)
# MCP工具现在自动可用于您的智能体！
```

### 🔧 **高级：MCPServerAdapter**（用于复杂场景）

对于需要手动连接管理的高级用例，`crewai-tools`库提供了`MCPServerAdapter`类。

我们目前支持以下传输机制：

* **Stdio**：用于本地服务器（通过同一台机器上进程之间的标准输入/输出进行通信）
* **服务器发送事件(SSE)**：用于远程服务器（通过HTTP从服务器到客户端的单向实时数据流）
* **可流式HTTPS**：用于远程服务器（通过HTTPS进行灵活的、可能是双向的通信，通常利用SSE进行服务器到客户端的流传输）

## 视频教程

观看此视频教程，了解CrewAI与MCP集成的综合指南：

<iframe className="w-full aspect-video rounded-xl" src="https://www.youtube.com/embed/TpQ45lAZh48" title="CrewAI MCP集成指南" frameBorder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowFullScreen />

## 安装

CrewAI MCP集成需要`mcp`库：

```shell  theme={null}
# 用于简单DSL集成（推荐）
uv add mcp

# 用于高级MCPServerAdapter使用
uv pip install 'crewai-tools[mcp]'
```

## 快速入门：简单DSL集成

集成MCP服务器的最简单方法是使用智能体上的`mcps`字段：

```python  theme={null}
from crewai import Agent, Task, Crew

# 创建带有MCP工具的智能体
research_agent = Agent(
    role="研究分析师",
    goal="使用高级搜索工具查找和分析信息",
    backstory="具有访问多个数据源权限的专家研究员",
    mcps=[
        "https://mcp.exa.ai/mcp?api_key=your_key&profile=your_profile",
        "crewai-amp:weather-service#current_conditions"
    ]
)

# 创建任务
research_task = Task(
    description="研究AI智能体框架的最新发展",
    expected_output="附带引用的综合研究报告",
    agent=research_agent
)

# 创建并运行团队
crew = Crew(agents=[research_agent], tasks=[research_task])
result = crew.kickoff()
```

就是这样！MCP工具会自动被发现并提供给您的智能体使用。

## MCP引用格式

`mcps`字段支持各种引用格式，以实现最大灵活性：

### 外部MCP服务器

```python  theme={null}
mcps=[
    # 完整服务器 - 获取所有可用工具
    "https://mcp.example.com/api",

    # 使用#语法从服务器获取特定工具
    "https://api.weather.com/mcp#get_current_weather",

    # 带有认证参数的服务器
    "https://mcp.exa.ai/mcp?api_key=your_key&profile=your_profile"
]
```

### CrewAI AMP市场

```python  theme={null}
mcps=[
    # 完整AMP MCP服务 - 获取所有可用工具
    "crewai-amp:financial-data",

    # 使用#语法从AMP服务获取特定工具
    "crewai-amp:research-tools#pubmed_search",

    # 多个AMP服务
    "crewai-amp:weather-service",
    "crewai-amp:market-analysis"
]
```

### 混合引用

```python  theme={null}
mcps=[
    "https://external-api.com/mcp",              # 外部服务器
    "https://weather.service.com/mcp#forecast",  # 特定外部工具
    "crewai-amp:financial-insights",             # AMP服务
    "crewai-amp:data-analysis#sentiment_tool"    # 特定AMP工具
]
```

## 主要特性

* 🔄 **自动工具发现**：工具会自动被发现并集成
* 🏷️ **名称冲突预防**：服务器名称会作为前缀添加到工具名称
* ⚡ **性能优化**：按需连接与模式缓存
* 🛡️ **错误恢复能力**：优雅处理不可用的服务器
* ⏱️ **超时保护**：内置超时防止连接挂起
* 📊 **透明集成**：与现有CrewAI特性无缝协作

## 错误处理

MCP DSL集成被设计为具有恢复能力：

```python  theme={null}
agent = Agent(
    role="有恢复能力的智能体",
    goal="即使服务器问题也继续工作",
    backstory="优雅处理失败的智能体",
    mcps=[
        "https://reliable-server.com/mcp",        # 将会工作
        "https://unreachable-server.com/mcp",     # 将会被优雅跳过
        "https://slow-server.com/mcp",            # 将会优雅超时
        "crewai-amp:working-service"              # 将会工作
    ]
)
# 智能体将使用工作服务器的工具，并为失败的工具记录警告
```

## 高级：MCPServerAdapter

对于需要手动连接管理的复杂场景，请使用`crewai-tools`中的`MCPServerAdapter`类。使用Python上下文管理器（`with`语句）是推荐的方法，因为它会自动处理与MCP服务器的连接启动和停止。

## 连接配置

`MCPServerAdapter`支持几个配置选项来自定义连接行为：

* **`connect_timeout`**（可选）：等待与MCP服务器建立连接的最大时间（秒）。如果未指定，默认为30秒。这对于响应时间可能不稳定的远程服务器特别有用。

```python  theme={null}
# 自定义连接超时示例
with MCPServerAdapter(server_params, connect_timeout=60) as tools:
    # 如果连接未建立，60秒后将会超时
    pass
```

```python  theme={null}
from crewai import Agent
from crewai_tools import MCPServerAdapter
from mcp import StdioServerParameters # 用于Stdio服务器

# 示例server_params（根据您的服务器类型选择一种）：
# 1. Stdio服务器：
server_params=StdioServerParameters(
    command="python3",
    args=["servers/your_server.py"],
    env={"UV_PYTHON": "3.12", **os.environ},
)

# 2. SSE服务器：
server_params = {
    "url": "http://localhost:8000/sse",
    "transport": "sse"
}

# 3. 可流式HTTP服务器：
server_params = {
    "url": "http://localhost:8001/mcp",
    "transport": "streamable-http"
}

# 示例用法（在设置server_params后取消注释并调整）：
with MCPServerAdapter(server_params, connect_timeout=60) as mcp_tools:
    print(f"可用工具: {[tool.name for tool in mcp_tools]}")

    my_agent = Agent(
        role="MCP工具用户",
        goal="利用MCP服务器的工具。",
        backstory="我可以连接到MCP服务器并使用它们的工具。",
        tools=mcp_tools, # 将加载的工具传递给您的智能体
        reasoning=True,
        verbose=True
    )
    # ... 其余团队设置 ...
```

这个通用模式展示了如何集成工具。对于针对每种传输方式定制的具体示例，请参考下面的详细指南。

## 过滤工具

有两种方法可以过滤工具：

1. 使用字典式索引访问特定工具。
2. 将工具名称列表传递给`MCPServerAdapter`构造函数。

### 使用字典式索引访问特定工具。

```python  theme={null}
with MCPServerAdapter(server_params, connect_timeout=60) as mcp_tools:
    print(f"可用工具: {[tool.name for tool in mcp_tools]}")

    my_agent = Agent(
        role="MCP工具用户",
        goal="利用MCP服务器的工具。",
        backstory="我可以连接到MCP服务器并使用它们的工具。",
        tools=[mcp_tools["tool_name"]], # 将加载的工具传递给您的智能体
        reasoning=True,
        verbose=True
    )
    # ... 其余团队设置 ...
```

### 将工具名称列表传递给`MCPServerAdapter`构造函数。

```python  theme={null}
with MCPServerAdapter(server_params, "tool_name", connect_timeout=60) as mcp_tools:
    print(f"可用工具: {[tool.name for tool in mcp_tools]}")

    my_agent = Agent(
        role="MCP工具用户",
        goal="利用MCP服务器的工具。",
        backstory="我可以连接到MCP服务器并使用它们的工具。",
        tools=mcp_tools, # 将加载的工具传递给您的智能体
        reasoning=True,
        verbose=True
    )
    # ... 其余团队设置 ...
```

## 与CrewBase一起使用

要在CrewBase类中使用MCPServer工具，请使用`get_mcp_tools`方法。服务器配置应通过`mcp_server_params`属性提供。您可以传递单个配置或多个服务器配置的列表。

```python  theme={null}
@CrewBase
class CrewWithMCP:
  # ... 定义您的智能体和任务配置文件 ...

  mcp_server_params = [
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

  @agent
  def your_agent(self):
      return Agent(config=self.agents_config["your_agent"], tools=self.get_mcp_tools()) # 获取所有可用工具

    # ... 其余团队设置 ...
```

<Tip>
  当crew类被`@CrewBase`装饰时，适配器生命周期会为您管理：

  * 第一次调用`get_mcp_tools()`会懒创建一个共享的`MCPServerAdapter`，该适配器会被crew中的每个智能体重用。
  * 适配器在`.kickoff()`完成后会自动关闭，这得益于`@CrewBase`注入的隐式after-kickoff钩子，因此无需手动清理。
  * 如果未定义`mcp_server_params`，`get_mcp_tools()`仅返回空列表，允许相同代码路径在有或没有MCP配置的情况下运行。

  这使得从多个智能体方法调用`get_mcp_tools()`或根据环境选择性地启用MCP是安全的。
</Tip>

### 连接超时配置

您可以通过设置`mcp_connect_timeout`类属性来配置MCP服务器的连接超时。如果未指定超时，则默认为30秒。

```python  theme={null}
@CrewBase
class CrewWithMCP:
  mcp_server_params = [...]
  mcp_connect_timeout = 60  # 所有MCP连接的超时时间为60秒

  @agent
  def your_agent(self):
      return Agent(config=self.agents_config["your_agent"], tools=self.get_mcp_tools())
```

```python  theme={null}
@CrewBase
class CrewWithDefaultTimeout:
  mcp_server_params = [...]
  # 未指定mcp_connect_timeout - 使用默认的30秒

  @agent
  def your_agent(self):
      return Agent(config=self.agents_config["your_agent"], tools=self.get_mcp_tools())
```

### 过滤工具

您可以通过将工具名称列表传递给`get_mcp_tools`方法来过滤哪些工具可用于您的智能体。

```python  theme={null}
@agent
def another_agent(self):
    return Agent(
      config=self.agents_config["your_agent"],
      tools=self.get_mcp_tools("tool_1", "tool_2") # 获取特定工具
    )
```

超时配置适用于crew中的所有MCP工具调用：

```python  theme={null}
@CrewBase
class CrewWithCustomTimeout:
  mcp_server_params = [...]
  mcp_connect_timeout = 90  # 所有MCP连接的超时时间为90秒

  @agent
  def filtered_agent(self):
      return Agent(
        config=self.agents_config["your_agent"],
        tools=self.get_mcp_tools("tool_1", "tool_2") # 带有自定义超时的特定工具
      )
```

## 探索MCP集成

<CardGroup cols={2}>
  <Card title="简单DSL集成" icon="code" href="/en/mcp/dsl-integration" color="#3B82F6">
    **推荐**：使用简单的`mcps=[]`字段语法进行轻松的MCP集成。
  </Card>

  <Card title="Stdio传输" icon="server" href="/en/mcp/stdio" color="#10B981">
    通过标准输入/输出连接到本地MCP服务器。适用于脚本和本地可执行文件。
  </Card>

  <Card title="SSE传输" icon="wifi" href="/en/mcp/sse" color="#F59E0B">
    使用服务器发送事件(SSE)与远程MCP服务器集成，实现实时数据流。
  </Card>

  <Card title="可流式HTTP传输" icon="globe" href="/en/mcp/streamable-http" color="#8B5CF6">
    利用灵活的可流式HTTP与远程MCP服务器进行稳健通信。
  </Card>

  <Card title="连接多个服务器" icon="layer-group" href="/en/mcp/multiple-servers" color="#EF4444">
    使用单个适配器同时聚合来自多个MCP服务器的工具。
  </Card>

  <Card title="安全考虑" icon="lock" href="/en/mcp/security" color="#DC2626">
    查看MCP集成的重要安全最佳实践，确保您的智能体安全。
  </Card>
</CardGroup>

查看此仓库，了解CrewAI与MCP集成的完整演示和示例！👇

<Card title="GitHub仓库" icon="github" href="https://github.com/tonykipkemboi/crewai-mcp-demo" target="_blank">
  CrewAI MCP演示
</Card>

## 与MCP保持安全

<Warning>
  在使用MCP服务器之前，请始终确保您信任它。
</Warning>

#### 安全警告：DNS重新绑定攻击

如果SSE传输没有得到适当保护，可能会受到DNS重新绑定攻击。
为防止这种情况：

1. **始终验证Origin头部**，确保传入的SSE连接来自预期的来源
2. **避免将服务器绑定到所有网络接口**(0.0.0.0)在本地运行时 - 而是仅绑定到localhost (127.0.0.1)
3. **为所有SSE连接实施适当的身份验证**

没有这些保护措施，攻击者可能会使用DNS重新绑定从远程网站与本地MCP服务器交互。

更多详情，请参阅[Anthropic的MCP传输安全文档](https://modelcontextprotocol.io/docs/concepts/transports#security-considerations)。

### 限制

* **支持的基元**：目前，`MCPServerAdapter`主要支持适配MCP`工具`。
  其他MCP基元（如`prompts`或`resources`）目前不通过此适配器直接集成为CrewAI组件。
* **输出处理**：适配器通常处理MCP工具的主要文本输出（例如，`.content[0].text`）。复杂或多模态输出可能需要自定义处理，如果不适合此模式。