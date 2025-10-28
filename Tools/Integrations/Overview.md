# 概述

> 将 CrewAI 智能体与外部自动化及托管 AI 服务相连接

集成工具允许您的智能体将工作移交给其他自动化平台和托管 AI 服务。当工作流需要调用现有的 CrewAI 部署或将专门的任务委托给 Amazon Bedrock 等提供商时，可以使用这些工具。

## **可用工具**

<CardGroup cols={2}>
  <Card title="CrewAI 运行自动化工具" icon="robot" href="/en/tools/integration/crewaiautomationtool">
    从您的智能体直接调用实时的 CrewAI 平台自动化，传递自定义输入，并轮询结果。
  </Card>

  <Card title="Bedrock 调用智能体工具" icon="aws" href="/en/tools/integration/bedrockinvokeagenttool">
    从您的团队中调用 Amazon Bedrock 智能体，复用 AWS 防护栏，并将响应流式传回工作流。
  </Card>
</CardGroup>

## **常见用例**

* **自动化链**：从另一个团队或流程中启动现有的 CrewAI 部署
* **企业级任务移交**：将任务路由到已封装公司逻辑和防护栏的 Bedrock 智能体
* **混合工作流**：将 CrewAI 推理与暴露其自身智能体 API 的下游系统相结合
* **长时间运行的任务**：轮询外部自动化，并将最终结果合并回当前运行中

## **快速入门示例**

```python  theme={null}
from crewai import Agent, Task, Crew
from crewai_tools import InvokeCrewAIAutomationTool
from crewai_tools.aws.bedrock.agents.invoke_agent_tool import BedrockInvokeAgentTool

# 外部自动化
analysis_automation = InvokeCrewAIAutomationTool(
    crew_api_url="https://analysis-crew.acme.crewai.com",
    crew_bearer_token="YOUR_BEARER_TOKEN",
    crew_name="Analysis Automation",
    crew_description="Runs the production-grade analysis pipeline",
)

# Bedrock 上的托管智能体
knowledge_router = BedrockInvokeAgentTool(
    agent_id="bedrock-agent-id",
    agent_alias_id="prod",
)

automation_strategist = Agent(
    role="Automation Strategist",
    goal="Orchestrate external automations and summarise their output",
    backstory="You coordinate enterprise workflows and know when to delegate tasks to specialised services.",
    tools=[analysis_automation, knowledge_router],
    verbose=True,
)

execute_playbook = Task(
    description="Run the analysis automation and ask the Bedrock agent for executive talking points.",
    agent=automation_strategist,
)

Crew(agents=[automation_strategist], tasks=[execute_playbook]).kickoff()
```

## **最佳实践**

* **保护凭证**：将 API 密钥和承载令牌存储在环境变量或密钥管理器中
* **规划延迟**：外部自动化可能耗时更长——请设置适当的轮询间隔和超时时间
* **复用会话**：Bedrock 智能体支持会话 ID，因此您可以在多个工具调用之间保持上下文
* **验证响应**：在将远程输出（JSON、文本、状态代码）转发给下游任务之前，请对其进行标准化处理
* **监控使用情况**：在 CrewAI 平台或 AWS CloudWatch 中跟踪审计日志，以提前了解配额限制和故障情况