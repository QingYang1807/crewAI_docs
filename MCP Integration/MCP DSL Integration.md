# MCP DSL 集成

> 了解如何使用 CrewAI 的简单 DSL 语法，通过 mcps 字段将 MCP 服务器直接集成到您的代理中。

## 概述

CrewAI 的 MCP DSL（领域特定语言）集成提供了将您的代理连接到 MCP（模型上下文协议）服务器的**最简单方式**。只需在您的代理中添加一个 `mcps` 字段，CrewAI 就会自动处理所有复杂性。

<Info>
  这是大多数 MCP 用例的**推荐方法**。对于需要手动连接管理的高级场景，请参见 [MCPServerAdapter](/en/mcp/overview#advanced-mcpserveradapter)。
</Info>

## 基本用法

使用 `mcps` 字段将 MCP 服务器添加到您的代理中：

```python  theme={null}
from crewai import Agent

agent = Agent(
    role="研究助理",
    goal="帮助研究和分析任务",
    backstory="拥有高级研究工具访问权限的专业助理",
    mcps=[
        "https://mcp.exa.ai/mcp?api_key=your_key&profile=research"
    ]
)

# MCP 工具现在自动可用！
# 无需手动连接管理或工具配置
```

## 支持的引用格式

### 外部 MCP 远程服务器

```python  theme={null}
# 基本 HTTPS 服务器
"https://api.example.com/mcp"

# 带认证的服务器
"https://mcp.exa.ai/mcp?api_key=your_key&profile=your_profile"

# 带自定义路径的服务器
"https://services.company.com/api/v1/mcp"
```

### 特定工具选择

使用 `#` 语法从服务器中选择特定工具：

```python  theme={null}
# 仅从天气服务器获取预报工具
"https://weather.api.com/mcp#get_forecast"

# 仅从 Exa 获取搜索工具
"https://mcp.exa.ai/mcp?api_key=your_key#web_search_exa"
```

### CrewAI AMP 市场

从 CrewAI AMP 市场访问工具：

```python  theme={null}
# 包含所有工具的完整服务
"crewai-amp:financial-data"

# AMP 服务的特定工具
"crewai-amp:research-tools#pubmed_search"

# 多个 AMP 服务
mcps=[
    "crewai-amp:weather-insights",
    "crewai-amp:market-analysis",
    "crewai-amp:social-media-monitoring"
]
```

## 完整示例

以下是使用多个 MCP 服务器的完整示例：

```python  theme={null}
from crewai import Agent, Task, Crew, Process

# 创建具有多个 MCP 源的代理
multi_source_agent = Agent(
    role="多源研究分析师",
    goal="使用多个数据源进行全面研究",
    backstory="""具有网络搜索、天气数据、财务信息
    和学术研究工具访问权限的专业研究员""",
    mcps=[
        # 外部 MCP 服务器
        "https://mcp.exa.ai/mcp?api_key=your_exa_key&profile=research",
        "https://weather.api.com/mcp#get_current_conditions",

        # CrewAI AMP 市场
        "crewai-amp:financial-insights",
        "crewai-amp:academic-research#pubmed_search",
        "crewai-amp:market-intelligence#competitor_analysis"
    ]
)

# 创建全面研究任务
research_task = Task(
    description="""研究 AI 代理对业务生产力的影响。
    包括远程工作的当前天气影响、金融市场趋势
    以及关于 AI 代理框架的最新学术出版物。""",
    expected_output="""全面报告涵盖：
    1. AI 代理业务影响分析
    2. 远程工作的天气考虑因素
    3. 与 AI 相关的金融市场趋势
    4. 学术研究引用和见解
    5. 竞争格局分析""",
    agent=multi_source_agent
)

# 创建并执行团队
research_crew = Crew(
    agents=[multi_source_agent],
    tasks=[research_task],
    process=Process.sequential,
    verbose=True
)

result = research_crew.kickoff()
print(f"研究完成，使用了 {len(multi_source_agent.mcps)} 个 MCP 数据源")
```

## 工具命名和组织

CrewAI 自动处理工具命名以防止冲突：

```python  theme={null}
# 原始 MCP 服务器有工具："search", "analyze"
# CrewAI 创建工具："mcp_exa_ai_search", "mcp_exa_ai_analyze"

agent = Agent(
    role="工具组织演示",
    goal="展示工具命名如何工作",
    backstory="演示自动工具组织",
    mcps=[
        "https://mcp.exa.ai/mcp?api_key=key",      # 工具：mcp_exa_ai_*
        "https://weather.service.com/mcp",         # 工具：weather_service_com_*
        "crewai-amp:financial-data"                # 工具：financial_data_*
    ]
)

# 每个服务器的工具根据服务器名称获得唯一前缀
# 这可以防止不同 MCP 服务器之间的命名冲突
```

## 错误处理和弹性

MCP DSL 设计为强大且用户友好：

### 优雅的服务器故障处理

```python  theme={null}
agent = Agent(
    role="弹性研究员",
    goal="即使遇到服务器问题也能进行研究",
    backstory="适应可用工具的资深研究员",
    mcps=[
        "https://primary-server.com/mcp",         # 主要数据源
        "https://backup-server.com/mcp",          # 主要失败时的备份
        "https://unreachable-server.com/mcp",     # 将被跳过并发出警告
        "crewai-amp:reliable-service"             # 可靠的 AMP 服务
    ]
)

# 代理将：
# 1. 成功连接到工作服务器
# 2. 为故障服务器记录警告
# 3. 使用可用工具继续
# 4. 不会因服务器故障而崩溃或挂起
```

### 超时保护

所有 MCP 操作都有内置超时：

* **连接超时**：10 秒
* **工具执行超时**：30 秒
* **发现超时**：15 秒

```python  theme={null}
# 如果无响应，这些服务器将优雅超时
mcps=[
    "https://slow-server.com/mcp",        # 如果无响应，10 秒后超时
    "https://overloaded-api.com/mcp"      # 如果发现时间 >15 秒，将超时
]
```

## 性能特性

### 自动缓存

工具架构缓存 5 分钟以提高性能：

```python  theme={null}
# 第一次创建代理 - 从服务器发现工具
agent1 = Agent(role="First", goal="Test", backstory="Test",
               mcps=["https://api.example.com/mcp"])

# 第二次创建代理（5 分钟内）- 使用缓存的工具架构
agent2 = Agent(role="Second", goal="Test", backstory="Test",
               mcps=["https://api.example.com/mcp"])  # 更快！
```

### 按需连接

工具连接仅在工具实际使用时建立：

```python  theme={null}
# 代理创建很快 - 尚未建立 MCP 连接
agent = Agent(
    role="按需代理",
    goal="高效使用工具",
    backstory="仅在需要时连接的高效代理",
    mcps=["https://api.example.com/mcp"]
)

# MCP 连接仅在工具实际执行时建立
# 这最大限度地减少了连接开销并提高了启动性能
```

## 与现有功能的集成

MCP 工具与其他 CrewAI 功能无缝协作：

```python  theme={null}
from crewai.tools import BaseTool

class CustomTool(BaseTool):
    name: str = "custom_analysis"
    description: str = "自定义分析工具"

    def _run(self, **kwargs):
        return "自定义分析结果"

agent = Agent(
    role="全功能代理",
    goal="使用所有可用工具类型",
    backstory="具有全面工具访问权限的代理",

    # 所有工具类型一起工作
    tools=[CustomTool()],                          # 自定义工具
    apps=["gmail", "slack"],                       # 平台集成
    mcps=[                                         # MCP 服务器
        "https://mcp.exa.ai/mcp?api_key=key",
        "crewai-amp:research-tools"
    ],

    verbose=True,
    max_iter=15
)
```

## 最佳实践

### 1. 尽可能使用特定工具

```python  theme={null}
# 好的做法 - 只获取您需要的工具
mcps=["https://weather.api.com/mcp#get_forecast"]

# 效率较低 - 从服务器获取所有工具
mcps=["https://weather.api.com/mcp"]
```

### 2. 安全地处理身份验证

```python  theme={null}
import os

# 在环境变量中存储 API 密钥
exa_key = os.getenv("EXA_API_KEY")
exa_profile = os.getenv("EXA_PROFILE")

agent = Agent(
    role="安全代理",
    goal="安全地使用 MCP 工具",
    backstory="安全意识强的代理",
    mcps=[f"https://mcp.exa.ai/mcp?api_key={exa_key}&profile={exa_profile}"]
)
```

### 3. 为服务器故障做准备

```python  theme={null}
# 始终包含备份选项
mcps=[
    "https://primary-api.com/mcp",       # 首选
    "https://backup-api.com/mcp",        # 备用选项
    "crewai-amp:reliable-service"        # AMP 后备
]
```

### 4. 使用描述性的代理角色

```python  theme={null}
agent = Agent(
    role="天气增强型市场分析师",
    goal="分析市场并考虑天气影响",
    backstory="具有天气数据访问权限的金融分析师，专攻农业市场洞察",
    mcps=[
        "https://weather.service.com/mcp#get_forecast",
        "crewai-amp:financial-data#stock_analysis"
    ]
)
```

## 故障排除

### 常见问题

**未发现工具：**

```python  theme={null}
# 检查您的 MCP 服务器 URL 和身份验证
# 验证服务器是否正在运行且可访问
mcps=["https://mcp.example.com/mcp?api_key=valid_key"]
```

**连接超时：**

```python  theme={null}
# 服务器可能运行缓慢或过载
# CrewAI 将记录警告并继续使用其他服务器
# 检查服务器状态或尝试备用服务器
```

**身份验证失败：**

```python  theme={null}
# 验证 API 密钥和凭据
# 查看服务器文档了解所需参数
# 确保查询参数已正确 URL 编码
```

## 高级：MCPServerAdapter

对于需要手动连接管理的复杂场景，请使用 `crewai-tools` 中的 `MCPServerAdapter` 类。推荐使用 Python 上下文管理器（`with` 语句），因为它会自动处理与 MCP 服务器的连接启动和停止。