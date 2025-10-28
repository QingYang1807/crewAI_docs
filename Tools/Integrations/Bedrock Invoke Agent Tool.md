# Bedrock 调用代理工具

> 使 CrewAI 代理能够调用 Amazon Bedrock 代理并在您的工作流中利用其功能

# `BedrockInvokeAgentTool`

`BedrockInvokeAgentTool` 使 CrewAI 代理能够调用 Amazon Bedrock 代理并在您的工作流中利用其功能。

## 安装

```bash  theme={null}
uv pip install 'crewai[tools]'
```

## 要求

* 已配置 AWS 凭证（通过环境变量或 AWS CLI）
* `boto3` 和 `python-dotenv` 包
* 访问 Amazon Bedrock 代理的权限

## 使用方法

以下是如何在 CrewAI 代理中使用该工具：

```python {2, 4-8} theme={null}
from crewai import Agent, Task, Crew
from crewai_tools.aws.bedrock.agents.invoke_agent_tool import BedrockInvokeAgentTool

# 初始化工具
agent_tool = BedrockInvokeAgentTool(
    agent_id="your-agent-id",
    agent_alias_id="your-agent-alias-id"
)

# 创建使用该工具的 CrewAI 代理
aws_expert = Agent(
    role='AWS 服务专家',
    goal='帮助用户理解 AWS 服务和配额',
    backstory='我是 AWS 服务专家，可以提供有关它们的详细信息。',
    tools=[agent_tool],
    verbose=True
)

# 为代理创建任务
quota_task = Task(
    description="查找 us-west-2 区域中 EC2 的当前服务配额并解释任何最近的变更。",
    agent=aws_expert
)

# 创建包含该代理的团队
crew = Crew(
    agents=[aws_expert],
    tasks=[quota_task],
    verbose=2
)

# 运行团队
result = crew.kickoff()
print(result)
```

## 工具参数

| 参数               | 类型    | 必需   | 默认值     | 描述                                   |
| :----------------- | :------ | :----- | :--------- | :------------------------------------ |
| **agent\_id**      | `str`   | 是     | None       | Bedrock 代理的唯一标识符              |
| **agent\_alias\_id** | `str`   | 是     | None       | 代理别名的唯一标识符                  |
| **session\_id**    | `str`   | 否     | timestamp  | 会话的唯一标识符                      |
| **enable\_trace**  | `bool`  | 否     | False      | 是否启用跟踪以进行调试                |
| **end\_session**   | `bool`  | 否     | False      | 是否在调用后结束会话                  |
| **description**    | `str`   | 否     | None       | 工具的自定义描述                      |

## 环境变量

```bash  theme={null}
BEDROCK_AGENT_ID=your-agent-id           # 替代传递 agent_id
BEDROCK_AGENT_ALIAS_ID=your-agent-alias-id # 替代传递 agent_alias_id
AWS_REGION=your-aws-region               # 默认为 us-west-2
AWS_ACCESS_KEY_ID=your-access-key        # AWS 身份验证所需
AWS_SECRET_ACCESS_KEY=your-secret-key    # AWS 身份验证所需
```

## 高级用法

### 带会话管理的多代理工作流

```python {2, 4-22} theme={null}
from crewai import Agent, Task, Crew, Process
from crewai_tools.aws.bedrock.agents.invoke_agent_tool import BedrockInvokeAgentTool

# 初始化带会话管理的工具
initial_tool = BedrockInvokeAgentTool(
    agent_id="your-agent-id",
    agent_alias_id="your-agent-alias-id",
    session_id="custom-session-id"
)

followup_tool = BedrockInvokeAgentTool(
    agent_id="your-agent-id",
    agent_alias_id="your-agent-alias-id",
    session_id="custom-session-id"
)

final_tool = BedrockInvokeAgentTool(
    agent_id="your-agent-id",
    agent_alias_id="your-agent-alias-id",
    session_id="custom-session-id",
    end_session=True
)

# 为不同阶段创建代理
researcher = Agent(
    role='AWS 服务研究员',
    goal='收集有关 AWS 服务的信息',
    backstory='我专门查找详细的 AWS 服务信息。',
    tools=[initial_tool]
)

analyst = Agent(
    role='服务兼容性分析师',
    goal='分析服务兼容性和要求',
    backstory='我分析 AWS 服务的兼容性和集成可能性。',
    tools=[followup_tool]
)

summarizer = Agent(
    role='技术文档撰写者',
    goal='创建清晰的技术摘要',
    backstory='我专门创建清晰、简洁的技术文档。',
    tools=[final_tool]
)

# 创建任务
research_task = Task(
    description="查找 us-west-2 区域中所有可用的 AWS 服务。",
    agent=researcher
)

analysis_task = Task(
    description="分析哪些服务支持 IPv6 及其实施要求。",
    agent=analyst
)

summary_task = Task(
    description="创建 IPv6 兼容服务及其主要功能的摘要。",
    agent=summarizer
)

# 创建包含代理和任务的团队
crew = Crew(
    agents=[researcher, analyst, summarizer],
    tasks=[research_task, analysis_task, summary_task],
    process=Process.sequential,
    verbose=2
)

# 运行团队
result = crew.kickoff()
```

## 用例

### 混合多代理协作

* 创建 CrewAI 代理与 AWS 中作为服务运行的托管 Bedrock 代理协作的工作流
* 实现敏感数据处理在您的 AWS 环境内进行，而其他代理在外部运行的场景
* 将本地 CrewAI 代理与基于云的 Bedrock 代理连接起来，实现分布式智能工作流

### 数据主权和合规性

* 将数据敏感的代理工作流保留在您的 AWS 环境中，同时允许外部 CrewAI 代理编排任务
* 通过仅在您的 AWS 账户内处理敏感信息来保持对数据驻留要求的合规性
* 在某些代理无法访问您组织的私人数据的情况下实现安全的多代理协作

### 无缝 AWS 服务集成

* 通过 Amazon Bedrock Actions 访问任何 AWS 服务，无需编写复杂的集成代码
* 使 CrewAI 代理能够通过自然语言请求与 AWS 服务交互
* 利用预先构建的 Bedrock 代理功能与 AWS 服务（如 Bedrock 知识库、Lambda 等）交互

### 可扩展的混合代理架构

* 将计算密集型任务卸载到托管 Bedrock 代理，而轻量级任务在 CrewAI 中运行
* 通过在工作负载本地 CrewAI 代理和基于云的 Bedrock 代理之间分配工作负载来扩展代理处理

### 跨组织代理协作

* 使您组织的 CrewAI 代理与合作伙伴组织的 Bedrock 代理之间能够安全协作
* 创建可以集成来自 Bedrock 代理的外部专业知识而无需暴露敏感数据的工作流
* 构建跨越组织边界的代理生态系统，同时保持安全性和数据控制