# CrewAI 文档

> 构建协作式 AI 智能体、团队和工作流 — 从第一天起即可投入生产。

<div
  style={{
  display: 'flex',
  flexDirection: 'column',
  alignItems: 'center',
  gap: 20,
  textAlign: 'center',
  padding: '48px 24px',
  borderRadius: 16,
  background: 'linear-gradient(180deg, rgba(235,102,88,0.12) 0%, rgba(201,76,60,0.08) 100%)',
  border: '1px solid rgba(235,102,88,0.18)'
}}
>
  <img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crew_only_logo.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=439ca5dc63a1768cad7196005ff5636f" alt="CrewAI" width="250" height="100" data-og-width="375" data-og-height="114" data-path="images/crew_only_logo.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crew_only_logo.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=ea0aa43c49a743b0e50cdc8e453f9150 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crew_only_logo.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=3025604ad4e1a40cda55cbb4ec726f14 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crew_only_logo.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=26b82b135ed2768dbb95a4f0ba4cd871 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crew_only_logo.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=77d06e853a60d4a862cbceecf1dd3e93 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crew_only_logo.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=da76ce1913c6086278df262cd9ad684a 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crew_only_logo.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=7b7cb283aa3588d52cdf6ed4c2e09d30 2500w" />

  <div style={{ maxWidth: 720 }}>
    <h1 style={{ marginBottom: 12 }}>自信地构建多智能体系统</h1>

    <p style={{ color: 'var(--mint-text-2)' }}>
      设计智能体、编排团队并自动化工作流，同时内置防护栏、记忆、知识库和可观测性功能。
    </p>
  </div>

  <div style={{ display: 'flex', flexWrap: 'wrap', gap: 12, justifyContent: 'center' }}>
    <a className="button button-primary" href="/en/quickstart">开始使用</a>
    <a className="button" href="/en/changelog">查看更新日志</a>
    <a className="button" href="/en/api-reference/introduction">API 参考</a>
  </div>
</div>

<div style={{ marginTop: 32 }} />

## 快速入门

<CardGroup cols={3}>
  <Card title="介绍" href="/en/introduction" icon="sparkles">
    CrewAI 的概念、架构概述，以及您可以使用智能体、团队和工作流构建的内容。
  </Card>

  <Card title="安装" href="/en/installation" icon="wrench">
    通过 `uv` 安装，配置 API 密钥，并为本地开发设置命令行界面（CLI）。
  </Card>

  <Card title="快速入门" href="/en/quickstart" icon="rocket">
    在几分钟内启动您的第一个团队。学习核心运行时、项目布局和开发循环。
  </Card>
</CardGroup>

## 构建基础

<CardGroup cols={3}>
  <Card title="智能体" href="/en/concepts/agents" icon="users">
    使用工具、记忆、知识库和 Pydantic 构建结构化输出来组合智能体。包含模板和最佳实践。
  </Card>

  <Card title="工作流" href="/en/concepts/flows" icon="arrow-progress">
    编排启动/监听/路由步骤，管理状态，持久化执行，以及恢复长时间运行的工作流。
  </Card>

  <Card title="任务与流程" href="/en/concepts/tasks" icon="check">
      定义顺序、分层或混合流程，并提供防护栏、回调和人机循环触发器。
  </Card>
</CardGroup>

## 企业应用之旅

<CardGroup cols={3}>
  <Card title="部署自动化" href="/en/enterprise/features/automations" icon="server">
    直接从企业控制台管理环境、安全地重新部署以及监控实时运行。
  </Card>

  <Card title="触发器与工作流" href="/en/enterprise/guides/automation-triggers" icon="bolt">
    连接 Gmail、Slack、Salesforce 等服务。自动将触发器有效负载传递到团队和工作流中。
  </Card>

  <Card title="团队管理" href="/en/enterprise/guides/team-management" icon="users-gear">
    邀请团队成员，配置基于角色的访问控制（RBAC），并控制对生产自动化的访问权限。
  </Card>
</CardGroup>

## 新增功能

<CardGroup cols={2}>
  <Card title="触发器概览" href="/en/enterprise/guides/automation-triggers" icon="sparkles">
    针对 Gmail、Drive、Outlook、Teams、OneDrive、HubSpot 等服务的统一概览 — 现包含示例有效负载和团队。
  </Card>

  <Card title="集成工具" href="/en/tools/integration/overview" icon="plug">
    使用更新的集成工具包，直接从您的团队中调用现有的 CrewAI 自动化或 Amazon Bedrock Agents。
  </Card>
</CardGroup>

<Callout title="探索真实世界模式" icon="github">
  浏览<a href="/en/examples/cookbooks">示例和食谱</a>，获取涵盖智能体、工作流和企业自动化的端到端参考实现。
</Callout>

## 保持联系

<CardGroup cols={2}>
  <Card title="在 GitHub 上为我们点赞" href="https://github.com/crewAIInc/crewAI" icon="star">
    如果 CrewAI 帮助您更快地交付项目，请给我们一个 Star（点赞），并与社区分享您的构建成果。
  </Card>

  <Card title="加入社区" href="https://community.crewai.com" icon="comments">
    与其他构建者一起提问、展示工作流、请求新功能。
  </Card>
</CardGroup>