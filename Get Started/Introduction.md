# 简介

> 构建协同工作的AIAgent团队，共同应对复杂任务

# 什么是CrewAI？

**CrewAI是一个精简、极速的Python框架，完全从零开始构建—完全独立于LangChain或其他Agent框架。**

CrewAI为开发者提供了高级简单性和精确的低级控制，非常适合创建适合任何场景的自主AIAgent：

* **[CrewAI团队(Crews)](/Guides/Guides/Build Your First Crew.md)**：优化自主性和协作智能，使您能够创建AI团队，其中每个Agent都有特定的角色、工具和目标。
* **[CrewAI流程(Flows)](/Guides/Guides/Build Your First Crew.md)**：实现细粒度的事件驱动控制，通过单个LLM调用进行精确的任务编排，并原生支持团队(Crews)。

通过我们的社区课程已有超过100,000名开发者获得认证，CrewAI正迅速成为企业级AI自动化的标准。

## 团队(Crews)如何工作

就像公司有各个部门（销售、工程、营销）在领导层协作下实现业务目标一样，CrewAI帮助您创建一个由具有专业化角色的AIAgent组成的组织，协作完成复杂任务。

**CrewAI框架概览**
<img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crews.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=514fd0b06e4128e62f10728d44601975" alt="CrewAI框架概览" data-og-width="634" width="634" data-og-height="473" height="473" data-path="images/crews.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crews.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=279c5c26c77fc9acc8411677716fa5ee 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crews.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=92b76be34b84b36771e0a8eed8976966 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crews.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=3ef573e6215967af1bb2975a58d0d859 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crews.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=1af6e6a337b70ca2ce238d8e40f49218 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crews.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=c5da01705f1373446f8582ac582ff244 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crews.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=96464aab7bb5efe4213a7b80f58038aa 2500w" />


| 组件         |            描述            | 关键特性                                                                                                                      |
| :----------- | :------------------------: | :-------------------------------------------------------------------------------------------------------------------------------- |
| **团队(Crew)** | 顶层组织结构              | • 管理AIAgent团队<br />• 监督工作流程<br />• 确保协作<br />• 交付结果                                                        |
| **AIAgent**   | 专业化团队成员            | • 具有特定角色（研究员、作家等）<br />• 使用指定工具<br />• 可委派任务<br />• 做出自主决策                           |
| **流程(Process)** | 工作流管理系统            | • 定义协作模式<br />• 控制任务分配<br />• 管理交互<br />• 确保高效执行                                               |
| **任务(Tasks)** | 个别分配的工作            | • 具有明确目标<br />• 使用特定工具<br />• 融入更大的流程<br />• 产生可操作的结果                                 |

### 它们如何协同工作

1. **团队(Crew)**组织整体运营
2. **AIAgent**处理其专业化任务
3. **流程(Process)**确保顺畅协作
4. **任务(Tasks)**完成以实现目标

## 关键特性

- 基于角色的Agent: 创建具有明确定义角色、专业知识和目标的专门Agent—从研究员到分析师再到作家
- 灵活工具: 为Agent配备自定义工具和API，与外部服务和数据源交互
- 智能协作: Agent协同工作，分享见解并协调任务以实现复杂目标
- 任务管理: 定义顺序或并行工作流，Agent自动处理任务依赖关系

## 流程(Flows)如何工作

虽然团队(Crews)在自主协作方面表现出色，但流程(Flows)提供结构化自动化，对工作流执行提供精细控制。流程确保任务可靠、安全、高效地执行，精确处理条件逻辑、循环和动态状态管理。流程与团队无缝集成，使您能够在高度自主性和精确控制之间取得平衡。

**CrewAI框架概览**
<img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/flows.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=82ea168de2f004553dcea21410cd7d8a" alt="CrewAI框架概览" data-og-width="669" width="669" data-og-height="464" height="464" data-path="images/flows.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/flows.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=4a6177acae3789dd8e4467b791c8966e 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/flows.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=7900e4cdad93fd37bbcd2f1f2f38b40b 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/flows.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=a83fa78165e93bc1d988a30ebc33889a 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/flows.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=540eb3d8d8f256d6d703aa5e6111a4cd 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/flows.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=04fbb8e23728b87efa78a0a776e2d194 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/flows.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=ff06d73f5d4aa911154c66becf14d732 2500w" />


| 组件           |               描述               | 关键特性                                                                                                                                                         |
| :------------- | :------------------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **流程(Flow)** | 结构化工作流编排                 | • 管理执行路径<br />• 处理状态转换<br />• 控制任务排序<br />• 确保可靠执行                                                                                         |
| **事件(Events)** | 工作流操作的触发器               | • 启动特定进程<br />• 启用动态响应<br />• 支持条件分支<br />• 允许实时适应                                                                                         |
| **状态(States)** | 工作流执行上下文                 | • 维护执行数据<br />• 启用持久性<br />• 支持可恢复性<br />• 确保执行完整性                                                                                         |
| **团队支持(Crew Support)** | 增强工作流自动化                 | • 在需要时注入自主性<br />• 补充结构化工作流<br />• 平衡自动化与智能<br />• 实现自适应决策-making                                                                       |

### 关键能力

<CardGroup cols={2}>
  <Card title="事件驱动编排" icon="bolt">
    定义精确的执行路径，动态响应事件
  </Card>

  <Card title="精细控制" icon="sliders">
    安全高效地管理工作流状态和条件执行
  </Card>

  <Card title="原生团队集成" icon="puzzle-piece">
    轻松与团队(Crews)结合以增强自主性和智能
  </Card>

  <Card title="确定性执行" icon="route">
    通过显式控制流和错误处理确保可预测的结果
  </Card>
</CardGroup>

## 何时使用团队(Crews)与流程(Flows)

理解何时使用[团队(Crews)](/en/guides/crews/first-crew)与[流程(Flows)](/en/guides/flows/first-flow)是在应用程序中最大化CrewAI潜力的关键。

| 使用案例             | 推荐方法                         | 原因                                                                                                                                        |
| :------------------- | :------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| **开放式研究**       | [团队(Crews)](/en/guides/crews/first-crew) | 当任务需要创造性思维、探索和适应时                                                                                                          |
| **内容生成**         | [团队(Crews)](/en/guides/crews/first-crew) | 用于协作创建文章、报告或营销材料                                                                                                            |
| **决策工作流**       | [流程(Flows)](/en/guides/flows/first-flow) | 当您需要具有精确控制的可预测、可审计的决策路径时                                                                                            |
| **API编排**          | [流程(Flows)](/en/guides/flows/first-flow) | 用于按特定顺序可靠集成多个外部服务                                                                                                          |
| **混合应用程序**     | 组合方法                         | 使用[流程(Flows)](/en/guides/flows/first-flow)编排整体流程，同时使用[团队(Crews)](/en/guides/crews/first-crew)处理复杂子任务               |

### 决策框架

* **选择[团队(Crews)](/en/guides/crews/first-crew)当：** 您需要自主问题解决、创造性协作或探索性任务
* **选择[流程(Flows)](/en/guides/flows/first-flow)当：** 您需要确定性结果、可审计性或对执行的精确控制
* **结合使用两者当：** 您的应用程序既需要结构化流程又需要自主智能

## 为什么选择CrewAI？

* 🧠 **自主运行**：Agent根据其角色和可用工具做出智能决策
* 📝 **自然交互**：Agent像人类团队成员一样交流和协作
* 🛠️ **可扩展设计**：易于添加新工具、角色和能力
* 🚀 **生产就绪**：为现实世界应用中的可靠性和可扩展性而构建
* 🔒 **安全专注**：考虑到企业安全需求而设计
* 💰 **成本高效**：优化以最小化令牌使用和API调用

## 准备开始构建了吗？

<a href="Guides/Crews/Build Your First Crew.md">构建您的第一个团队(Crew)：分步教程，创建协作的AI团队，共同解决复杂问题。</a>
<a href="Guides/Flows/Build Your First Flow.md">构建您的第一个流程(Flow)：学习如何创建结构化的、事件驱动的工作流，精确控制执行。</a>
<a href="Get Started/Installation.md">安装CrewAI:在您的开发环境中开始使用CrewAI。</a>
<a href="Get Started/Quickstart.md">快速入门：遵循我们的快速入门指南，创建您的第一个CrewAIAgent并获得实践经验。</a>
<a href="https://community.crewai.com">与其他开发者联系，获取帮助，分享您的CrewAI经验。</a>