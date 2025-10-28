# Stagehand 工具

> 将 Stagehand 与 CrewAI 集成的网页自动化工具，用于浏览器交互和自动化

# 概述

`StagehandTool` 将 [Stagehand](https://docs.stagehand.dev/get_started/introduction) 框架与 CrewAI 集成，使代理能够使用自然语言指令与网站交互并自动化浏览器任务。

## 概述

Stagehand 是由 Browserbase 构建的强大浏览器自动化框架，允许 AI 代理：

* 导航到网站
* 点击按钮、链接和其他元素
* 填写表单
* 从网页提取数据
* 观察和识别元素
* 执行复杂工作流程

StagehandTool 封装了 Stagehand Python SDK，通过三个核心原语为 CrewAI 代理提供浏览器控制能力：

1. **执行（Act）**：执行点击、输入或导航等操作
2. **提取（Extract）**：从网页提取结构化数据
3. **观察（Observe）**：识别和分析页面上的元素

## 先决条件

使用此工具前，请确保您具备：

1. 拥有 [Browserbase](https://www.browserbase.com/) 账户，并获取 API 密钥和项目 ID
2. 大语言模型（OpenAI 或 Anthropic Claude）的 API 密钥
3. 已安装 Stagehand Python SDK

安装所需的依赖：

```bash  theme={null}
pip install stagehand-py
```

## 使用方法

### 基本实现

StagehandTool 可以通过两种方式实现：

#### 1. 使用上下文管理器（推荐）

<Tip>
  上下文管理器方法是推荐的，因为它即使在发生异常时也能确保正确清理资源。
</Tip>

```python  theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import StagehandTool
from stagehand.schemas import AvailableModel

# 使用上下文管理器通过您的 API 密钥初始化工具
with StagehandTool(
    api_key="your-browserbase-api-key",
    project_id="your-browserbase-project-id",
    model_api_key="your-llm-api-key",  # OpenAI 或 Anthropic API 密钥
    model_name=AvailableModel.CLAUDE_3_7_SONNET_LATEST,  # 可选：指定使用的模型
) as stagehand_tool:
    # 创建带有工具的代理
    researcher = Agent(
        role="Web Researcher",
        goal="Find and summarize information from websites",
        backstory="I'm an expert at finding information online.",
        verbose=True,
        tools=[stagehand_tool],
    )

    # 创建使用该工具的任务
    research_task = Task(
        description="Go to https://www.example.com and tell me what you see on the homepage.",
        agent=researcher,
    )

    # 运行团队
    crew = Crew(
        agents=[researcher],
        tasks=[research_task],
        verbose=True,
    )

    result = crew.kickoff()
    print(result)
```

#### 2. 手动资源管理

```python  theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import StagehandTool
from stagehand.schemas import AvailableModel

# 使用您的 API 密钥初始化工具
stagehand_tool = StagehandTool(
    api_key="your-browserbase-api-key",
    project_id="your-browserbase-project-id",
    model_api_key="your-llm-api-key",
    model_name=AvailableModel.CLAUDE_3_7_SONNET_LATEST,
)

try:
    # 创建带有工具的代理
    researcher = Agent(
        role="Web Researcher",
        goal="Find and summarize information from websites",
        backstory="I'm an expert at finding information online.",
        verbose=True,
        tools=[stagehand_tool],
    )

    # 创建使用该工具的任务
    research_task = Task(
        description="Go to https://www.example.com and tell me what you see on the homepage.",
        agent=researcher,
    )

    # 运行团队
    crew = Crew(
        agents=[researcher],
        tasks=[research_task],
        verbose=True,
    )

    result = crew.kickoff()
    print(result)
finally:
    # 显式清理资源
    stagehand_tool.close()
```

## 命令类型

StagehandTool 支持三种不同的命令类型，用于特定的网页自动化任务：

### 1. 执行命令（Act Command）

`act` 命令类型（默认）支持点击按钮、填写表单和导航等网页交互。

```python  theme={null}
# 执行操作（默认行为）
result = stagehand_tool.run(
    instruction="点击登录按钮", 
    url="https://example.com",
    command_type="act"  # 默认值，可以省略
)

# 填写表单
result = stagehand_tool.run(
    instruction="使用姓名 'John Doe'、邮箱 'john@example.com' 和消息 'Hello world' 填写联系表单", 
    url="https://example.com/contact"
)
```

### 2. 提取命令（Extract Command）

`extract` 命令类型从网页检索结构化数据。

```python  theme={null}
# 提取所有产品信息
result = stagehand_tool.run(
    instruction="提取所有产品名称、价格和描述", 
    url="https://example.com/products",
    command_type="extract"
)

# 使用选择器提取特定信息
result = stagehand_tool.run(
    instruction="提取文章标题和主要内容", 
    url="https://example.com/blog/article",
    command_type="extract",
    selector=".article-container"  # 可选的 CSS 选择器
)
```

### 3. 观察命令（Observe Command）

`observe` 命令类型识别和分析网页元素。

```python  theme={null}
# 查找交互元素
result = stagehand_tool.run(
    instruction="在导航菜单中查找所有交互元素", 
    url="https://example.com",
    command_type="observe"
)

# 识别表单字段
result = stagehand_tool.run(
    instruction="识别注册表单中的所有输入字段", 
    url="https://example.com/register",
    command_type="observe",
    selector="#registration-form"
)
```

## 配置选项

使用这些参数自定义 StagehandTool 的行为：

```python  theme={null}
stagehand_tool = StagehandTool(
    api_key="your-browserbase-api-key",
    project_id="your-browserbase-project-id",
    model_api_key="your-llm-api-key",
    model_name=AvailableModel.CLAUDE_3_7_SONNET_LATEST,
    dom_settle_timeout_ms=5000,  # 等待 DOM 稳定的时间更长
    headless=True,  # 在无头模式下运行浏览器
    self_heal=True,  # 尝试从错误中恢复
    wait_for_captcha_solves=True,  # 等待验证码解决
    verbose=1,  # 控制日志详细程度（0-3）
)
```

## 最佳实践

1. **具体明确**：提供详细指令以获得更好结果
2. **选择适当的命令类型**：为您的任务选择正确的命令类型
3. **使用选择器**：利用 CSS 选择器提高准确性
4. **分解复杂任务**：将复杂工作流程拆分为多个工具调用
5. **实现错误处理**：为潜在问题添加错误处理

## 故障排除

常见问题和解决方案：

* **会话问题**：验证 Browserbase 和 LLM 提供商的 API 密钥
* **元素未找到**：为较慢的页面增加 `dom_settle_timeout_ms`
* **操作失败**：首先使用 `observe` 识别正确的元素
* **数据不完整**：优化指令或提供特定的选择器

## 其他资源

有关 CrewAI 集成的问题：

* 加入 Stagehand 的 [Slack 社区](https://stagehand.dev/slack)
* 在 [Stagehand 仓库](https://github.com/browserbase/stagehand) 中提出问题
* 访问 [Stagehand 文档](https://docs.stagehand.dev/)