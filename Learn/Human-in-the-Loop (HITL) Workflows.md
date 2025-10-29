# 人机协同（HITL）工作流程

> 了解如何在 CrewAI 中实现人机协同工作流程，以增强决策能力

人机协同（HITL）是一种强大的方法，它将人工智能与人类专业知识相结合，以提高决策质量和改善任务结果。本指南向您展示如何在 CrewAI 中实现 HITL。

## 设置 HITL 工作流程


**配置您的任务**

设置启用人工输入的任务：

<img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-human-input.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=cb2e2bab131e9eff86b0c51dceb16e11" alt="Crew 人工输入" data-og-width="624" width="624" data-og-height="165" height="165" data-path="images/enterprise/crew-human-input.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-human-input.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=1bc2a85e5aa6e736a118fe2c91452dc6 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-human-input.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=137c8e9c09c9a93ba1b683ad3e247e0d 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-human-input.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=79c8be91790b117c1498568ca48f4287 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-human-input.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=4da8411c0c26ee98c0dcdde6117353fe 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-human-input.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=1b24b707df7ec697db2652d80ed3ff8f 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-human-input.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=39a7543043c397cf4ff84582216ddb65 2500w" />

**提供 Webhook URL**

启动您的 crew 时，包含一个人工输入的 webhook URL：

<img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-webhook-url.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=f2d298c0b4c7b3a62e1dee4e2e6f1bb3" alt="Crew Webhook URL" data-og-width="624" width="624" data-og-height="259" height="259" data-path="images/enterprise/crew-webhook-url.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-webhook-url.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=80f52cbe2cd1c6a2a4cd3e2039c22971 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-webhook-url.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=6496d6f5e1fe13fec8be8a406e635b26 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-webhook-url.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=27cfbbf1fecdab2540df4aeb7ddd15b6 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-webhook-url.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=57d3439e96917a0627189bfd188af4a0 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-webhook-url.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=cad1f034d8fd4113f08df6bf1a58f3fa 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-webhook-url.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=fba10cd375c57bcd9b2a216067b5bd44 2500w" />

使用 Bearer 认证的示例：

```bash
curl -X POST {BASE_URL}/kickoff \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "inputs": {
      "topic": "AI 研究"
    },
    "humanInputWebhook": {
      "url": "https://your-webhook.com/hitl",
      "authentication": {
        "strategy": "bearer",
        "token": "your-webhook-secret-token"
      }
    }
  }'
```

 或使用 Basic 认证：

 ```bash
scurl -X POST {BASE_URL}/kickoff \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "inputs": {
      "topic": "AI 研究"
    },
    "humanInputWebhook": {
      "url": "https://your-webhook.com/hitl",
      "authentication": {
        "strategy": "basic",
        "username": "your-username",
        "password": "your-password"
      }
    }
  }'
 ```

**接收 Webhook 通知**
  
当 crew 完成需要人工输入的任务后，您将收到包含以下内容的 webhook 通知：

  * 执行 ID
  * 任务 ID
  * 任务输出

**审查任务输出**

系统将在"等待人工输入"状态下暂停。仔细审查任务输出。

**提交人工反馈**

使用以下信息调用您 crew 的恢复端点：

<img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-resume-endpoint.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=1e1c2ca22a2d674426f8e663fed33eca" alt="Crew 恢复端点" data-og-width="624" width="624" data-og-height="261" height="261" data-path="images/enterprise/crew-resume-endpoint.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-resume-endpoint.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=09014207ae06e6522303b77e4648f0d4 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-resume-endpoint.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=1ad53990ab04014e622b3acdb37ca604 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-resume-endpoint.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=afb11308edffa03de969712505cf95ab 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-resume-endpoint.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=9bd69f0d75ec47ac2c6280f24a550bff 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-resume-endpoint.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=f81e1ebcdc8a9348133503eb5eb4e37a 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-resume-endpoint.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=b12843fa2b80cc86580220766a1f4cc4 2500w" />

> **重要：必须再次提供 Webhook URL**：
> 您**必须**在恢复调用中提供与启动调用中使用的相同的 webhook URL（`taskWebhookUrl`、`stepWebhookUrl`、`crewWebhookUrl`）。
> Webhook 配置**不会**从启动自动延续 - 它们必须明确包含在恢复请求中，以继续接收任务完成、代理步骤和 crew 完>成的通知。
带有 webhooks 的恢复调用示例：

```bash
curl -X POST {BASE_URL}/resume \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "execution_id": "abcd1234-5678-90ef-ghij-klmnopqrstuv",
    "task_id": "research_task",
    "human_feedback": "做得很好！请添加更多细节。",
    "is_approve": true,
    "taskWebhookUrl": "https://your-server.com/webhooks/task",
    "stepWebhookUrl": "https://your-server.com/webhooks/step",
    "crewWebhookUrl": "https://your-server.com/webhooks/crew"
  }'
```

  **反馈对任务执行的影响**：
  提供反馈时必须谨慎，因为整个反馈内容将作为额外上下文纳入进一步的任务执行中。

  这意味着：

  * 反馈中的所有信息都将成为任务上下文的一部分。
  * 无关的细节可能会对其产生负面影响。
  * 简洁、相关的反馈有助于保持任务的焦点和效率。
  * 在提交之前务必仔细检查您的反馈，确保其只包含能积极指导任务执行的相关信息。

**处理负面反馈**
如果您提供负面反馈：

* crew 将使用您反馈中的额外上下文重试该任务。
* 您将收到另一个 webhook 通知以供进一步审查。
* 重复步骤 4-6，直到满意为止。

**继续执行**
当您提交正面反馈时，执行将继续进行下一步。

## 最佳实践

* **具体明确**：提供清晰、可操作的反馈，直接解决当前任务。
* **保持相关性**：只包含有助于改善任务执行的信息。
* **及时响应**：及时响应 HITL 提示，避免工作流程延迟。
* **仔细审查**：提交前仔细检查您的反馈，确保准确性。

## 常见用例

HITL 工作流程在以下情况下特别有价值：

* 质量保证和验证
* 复杂的决策场景
* 敏感或高风险操作
* 需要人类判断的创意任务
* 合规性和监管审查