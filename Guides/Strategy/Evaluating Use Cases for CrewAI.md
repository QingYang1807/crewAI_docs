# CrewAI 使用场景评估

> 了解如何评估您的 AI 应用需求，并根据复杂性和精度要求在 Crews 和 Flows 之间选择合适的方法。

## 理解决策框架

在使用 CrewAI 构建 AI 应用时，您将做出的最重要决策之一是为您的特定用例选择合适的方法。您应该使用 Crew 吗？还是 Flow？或者两者结合？本指南将帮助您评估需求并做出明智的架构决策。

这个决策的核心是理解您的应用中**复杂性**和**精确性**之间的关系：

<Frame caption="CrewAI 应用的复杂性与精确性矩阵">
  <img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/complexity_precision.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=ba6f265da6ac72075285b5008735be82" alt="复杂性与精确性矩阵" data-og-width="615" width="615" data-og-height="392" height="392" data-path="images/complexity_precision.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/complexity_precision.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=48c8a451aaef57f3f152ccb921dac715 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/complexity_precision.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=780bae03e53c2fcfd4dce5e3c8672372 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/complexity_precision.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=364f719dfe67f2ae8a54ffce0a7544a8 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/complexity_precision.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=535a75d70cd4109123adf1d34e1316a0 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/complexity_precision.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=4f42346c0b66928bf27b3c3a78a3a6ff 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/complexity_precision.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=832ce0a91bf333433801bca1d1832527 2500w" />
</Frame>

这个矩阵帮助可视化不同的方法如何与复杂性和精确性的不同需求相匹配。让我们探讨每个象限的含义以及它如何指导您的架构选择。

## 复杂性-精确性矩阵解析

### 什么是复杂性？

在 CrewAI 应用的上下文中，**复杂性**指的是：

* 所需的不同步骤或操作的数量
* 需要执行的任务的多样性
* 不同组件之间的相互依赖关系
* 对条件逻辑和分支的需求
* 整体工作流程的复杂程度

### 什么是精确性？

在此背景下，**精确性**指的是：

* 最终输出所需的准确性
* 对结构化、可预测结果的需求
* 可重复性的重要性
* 对每个步骤的控制程度
* 对输出变化的容忍度

### 四个象限

#### 1. 低复杂性，低精确性

**特点：**

* 简单、直接的任务
* 对输出中的变化有一定容忍度
* 有限的步骤数
* 创意或探索性应用

**推荐方法：**具有最少代理的简单 Crews

**示例用例：**

* 基础内容生成
* 头脑风暴
* 简单的总结任务
* 创意写作辅助

#### 2. 低复杂性，高精确性

**特点：**

* 需要精确、结构化输出的简单工作流程
* 对可重复结果的需求
* 步骤有限但准确性要求高
* 通常涉及数据处理或转换

**推荐方法：**具有直接 LLM 调用的 Flows 或具有结构化输出的简单 Crews

**示例用例：**

* 数据提取和转换
* 表单填写和验证
* 结构化内容生成（JSON、XML）
* 简单的分类任务

#### 3. 高复杂性，低精确性

**特点：**

* 包含多个步骤的多阶段过程
* 创意或探索性输出
* 组件之间的复杂交互
* 对最终结果的变化有容忍度

**推荐方法：**具有多个专业化代理的复杂 Crews

**示例用例：**

* 研究和分析
* 内容创建流程
* 探索性数据分析
* 创意问题解决

#### 4. 高复杂性，高精确性

**特点：**

* 需要结构化输出的复杂工作流程
* 具有严格准确性要求的多个相互依赖的步骤
* 对复杂处理和精确结果的双重需求
* 通常涉及关键任务应用

**推荐方法：**协调多个 Crews 并具有验证步骤的 Flows

**示例用例：**

* 企业决策支持系统
* 复杂数据处理管道
* 多阶段文档处理
* 受监管行业的应用

## 在 Crews 和 Flows 之间选择

### 何时选择 Crews

Crews 在以下情况下是理想选择：

1. **您需要协作智能** - 具有不同专业知识的多个代理需要一起工作
2. **问题需要涌现思维** - 解决方案受益于不同的观点和方法
3. **任务主要是创意性或分析性的** - 工作涉及研究、内容创建或分析
4. **您重视适应性而非严格结构** - 工作流程可以从代理自主性中受益
5. **输出格式可以有一定的灵活性** - 输出结构的一些变化是可以接受的

```python  theme={null}
# 示例：用于市场分析的研究 Crew
from crewai import Agent, Crew, Process, Task

# 创建专业化代理
researcher = Agent(
    role="市场研究专家",
    goal="查找新兴技术的全面市场数据",
    backstory="您是发现市场趋势和收集数据的专家。"
)

analyst = Agent(
    role="市场分析师",
    goal="分析市场数据并识别关键机会",
    backstory="您擅长解读市场数据和发现有价值的见解。"
)

# 定义他们的任务
research_task = Task(
    description="研究人工智能医疗解决方案的当前市场格局",
    expected_output="包括主要参与者、市场规模和增长趋势的全面市场数据",
    agent=researcher
)

analysis_task = Task(
    description="分析市场数据并确定前3个投资机会",
    expected_output="包含3个推荐投资机会及其理由的分析报告",
    agent=analyst,
    context=[research_task]
)

# 创建 crew
market_analysis_crew = Crew(
    agents=[researcher, analyst],
    tasks=[research_task, analysis_task],
    process=Process.sequential,
    verbose=True
)

# 运行 crew
result = market_analysis_crew.kickoff()
```

### 何时选择 Flows

Flows 在以下情况下是理想选择：

1. **您需要精确控制执行** - 工作流程需要精确的排序和状态管理
2. **应用具有复杂的状态要求** - 您需要在多个步骤之间维护和转换状态
3. **您需要结构化、可预测的输出** - 应用需要一致的、格式化的结果
4. **工作流程涉及条件逻辑** - 需要根据中间结果采用不同的路径
5. **您需要将 AI 与过程代码结合** - 解决方案需要 AI 能力和传统编程两者

```python  theme={null}
# 示例：具有结构化处理的客户支持 Flow
from crewai.flow.flow import Flow, listen, router, start
from pydantic import BaseModel
from typing import List, Dict

# 定义结构化状态
class SupportTicketState(BaseModel):
    ticket_id: str = ""
    customer_name: str = ""
    issue_description: str = ""
    category: str = ""
    priority: str = "medium"
    resolution: str = ""
    satisfaction_score: int = 0

class CustomerSupportFlow(Flow[SupportTicketState]):
    @start()
    def receive_ticket(self):
        # 在实际应用中，这可能来自 API
        self.state.ticket_id = "TKT-12345"
        self.state.customer_name = "Alex Johnson"
        self.state.issue_description = "付款后无法访问高级功能"
        return "已收到工单"

    @listen(receive_ticket)
    def categorize_ticket(self, _):
        # 使用直接 LLM 调用进行分类
        from crewai import LLM
        llm = LLM(model="openai/gpt-4o-mini")

        prompt = f"""
        将以下客户支持问题分类为以下类别之一：
        - 计费
        - 账户访问
        - 技术问题
        - 功能请求
        - 其他

        问题：{self.state.issue_description}

        只返回类别名称。
        """

        self.state.category = llm.call(prompt).strip()
        return self.state.category

    @router(categorize_ticket)
    def route_by_category(self, category):
        # 根据类别路由到不同的处理程序
        return category.lower().replace(" ", "_")

    @listen("billing")
    def handle_billing_issue(self):
        # 处理计费特定逻辑
        self.state.priority = "high"
        # 更多计费特定处理...
        return "计费问题已处理"

    @listen("account_access")
    def handle_access_issue(self):
        # 处理访问特定逻辑
        self.state.priority = "high"
        # 更多访问特定处理...
        return "访问问题已处理"

    # 其他类别处理程序...

    @listen("billing", "account_access", "technical_issue", "feature_request", "other")
    def resolve_ticket(self, resolution_info):
        # 最终解决步骤
        self.state.resolution = f"问题已解决：{resolution_info}"
        return self.state.resolution

# 运行 flow
support_flow = CustomerSupportFlow()
result = support_flow.kickoff()
```

### 何时结合 Crews 和 Flows

最复杂的应用通常受益于结合 Crews 和 Flows：

1. **复杂的多阶段过程** - 使用 Flows 协调整体过程，使用 Crews 处理复杂子任务
2. **需要创造力和结构的应用** - 使用 Crews 进行创意任务，使用 Flows 进行结构化处理
3. **企业级 AI 应用** - 使用 Flows 管理状态和流程，同时利用 Crews 进行专业工作

```python  theme={null}
# 示例：结合 Crews 和 Flows 的内容生产管道
from crewai.flow.flow import Flow, listen, start
from crewai import Agent, Crew, Process, Task
from pydantic import BaseModel
from typing import List, Dict

class ContentState(BaseModel):
    topic: str = ""
    target_audience: str = ""
    content_type: str = ""
    outline: Dict = {}
    draft_content: str = ""
    final_content: str = ""
    seo_score: int = 0

class ContentProductionFlow(Flow[ContentState]):
    @start()
    def initialize_project(self):
        # 设置初始参数
        self.state.topic = "可持续投资"
        self.state.target_audience = "千禧一代投资者"
        self.state.content_type = "博客文章"
        return "项目已初始化"

    @listen(initialize_project)
    def create_outline(self, _):
        # 使用研究 crew 创建大纲
        researcher = Agent(
            role="内容研究员",
            goal=f"为{self.state.target_audience}研究{self.state.topic}",
            backstory="您是具有丰富内容创建知识的专业研究员。"
        )

        outliner = Agent(
            role="内容策略师",
            goal=f"为{self.state.content_type}创建引人入胜的大纲",
            backstory="您擅长构建内容以获得最大参与度。"
        )

        research_task = Task(
            description=f"研究{self.state.topic}，重点关注{self.state.target_audience}感兴趣的内容",
            expected_output="包含关键点和统计数据的综合研究笔记",
            agent=researcher
        )

        outline_task = Task(
            description=f"为关于{self.state.topic}的{self.state.content_type}创建大纲",
            expected_output="包含部分和要点的详细内容大纲",
            agent=outliner,
            context=[research_task]
        )

        outline_crew = Crew(
            agents=[researcher, outliner],
            tasks=[research_task, outline_task],
            process=Process.sequential,
            verbose=True
        )

        # 运行 crew 并存储结果
        result = outline_crew.kickoff()

        # 解析大纲（在实际应用中，您可能会使用更健壮的解析方法）
        import json
        try:
            self.state.outline = json.loads(result.raw)
        except:
            # 如果不是有效的 JSON 则使用后备方案
            self.state.outline = {"sections": result.raw}

        return "大纲已创建"

    @listen(create_outline)
    def write_content(self, _):
        # 使用写作 crew 创建内容
        writer = Agent(
            role="内容作家",
            goal=f"为{self.state.target_audience}撰写引人入胜的内容",
            backstory="您是创作引人入胜内容的 skilled 作家。"
        )

        editor = Agent(
            role="内容编辑",
            goal="确保内容经过精心打磨、准确且引人入胜",
            backstory="您对细节有敏锐的眼光，并有提高内容的才能。"
        )

        writing_task = Task(
            description=f"按照此大纲撰写关于{self.state.topic}的{self.state.content_type}：{self.state.outline}",
            expected_output="以 markdown 格式呈现的完整草稿内容",
            agent=writer
        )

        editing_task = Task(
            description="编辑和改进草稿内容，以提高清晰度、吸引力和准确性",
            expected_output="以 markdown 格式呈现的精心打磨的最终内容",
            agent=editor,
            context=[writing_task]
        )

        writing_crew = Crew(
            agents=[writer, editor],
            tasks=[writing_task, editing_task],
            process=Process.sequential,
            verbose=True
        )

        # 运行 crew 并存储结果
        result = writing_crew.kickoff()
        self.state.final_content = result.raw

        return "内容已创建"

    @listen(write_content)
    def optimize_for_seo(self, _):
        # 使用直接 LLM 调用进行 SEO 优化
        from crewai import LLM
        llm = LLM(model="openai/gpt-4o-mini")

        prompt = f"""
        针对关键词"{self.state.topic}"分析此内容的 SEO 效果。
        以 1-100 的标准评分，并提供 3 个具体的改进建议。

        内容：{self.state.final_content[:1000]}... (为简洁起见截断)

        将您的回答格式化为 JSON，具有以下结构：
        {{
            "score": 85,
            "recommendations": [
                "建议1",
                "建议2",
                "建议3"
            ]
        }}
        """

        seo_analysis = llm.call(prompt)

        # 解析 SEO 分析
        import json
        try:
            analysis = json.loads(seo_analysis)
            self.state.seo_score = analysis.get("score", 0)
            return analysis
        except:
            self.state.seo_score = 50
            return {"score": 50, "recommendations": ["无法解析 SEO 分析"]}

# 运行 flow
content_flow = ContentProductionFlow()
result = content_flow.kickoff()
```

## 实用评估框架

要确定适合您特定用例的正确方法，请按照这个逐步评估框架进行：

### 第 1 步：评估复杂性

通过考虑以下因素，以 1-10 的标准对您应用的复杂性进行评分：

1. **步骤数量**：需要多少不同的操作？
   * 1-3 步：低复杂性 (1-3)
   * 4-7 步：中等复杂性 (4-7)
   * 8+ 步：高复杂性 (8-10)

2. **相互依赖性**：不同部分之间的联系有多紧密？
   * 少量依赖：低复杂性 (1-3)
   * 一些依赖：中等复杂性 (4-7)
   * 许多复杂依赖：高复杂性 (8-10)

3. **条件逻辑**：需要多少分支和决策？
   * 线性过程：低复杂性 (1-3)
   * 一些分支：中等复杂性 (4-7)
   * 复杂决策树：高复杂性 (8-10)

4. **领域知识**：所需的知识有多专业化？
   * 一般知识：低复杂性 (1-3)
   * 一些专业知识：中等复杂性 (4-7)
   * 多个领域的深度专业知识：高复杂性 (8-10)

计算您的平均分数以确定总体复杂性。

### 第 2 步：评估精确性要求

通过考虑以下因素，以 1-10 的标准对您的精确性要求进行评分：

1. **输出结构**：输出的结构化程度必须多高？
   * 自由格式文本：低精确性 (1-3)
   * 半结构化：中等精确性 (4-7)
   * 严格格式化（JSON、XML）：高精确性 (8-10)

2. **准确性需求**：事实准确性有多重要？
   * 创意内容：低精确性 (1-3)
   * 信息性内容：中等精确性 (4-7)
   * 关键信息：高精确性 (8-10)

3. **可重复性**：不同运行之间的结果必须有多一致？
   * 变化可接受：低精确性 (1-3)
   * 需要一些一致性：中等精确性 (4-7)
   * 需要精确可重复性：高精确性 (8-10)

4. **错误容忍度**：错误的影响是什么？
   * 低影响：低精确性 (1-3)
   * 中等影响：中等精确性 (4-7)
   * 高影响：高精确性 (8-10)

计算您的平均分数以确定总体精确性要求。

### 第 3 步：映射到矩阵

将您的复杂性和精确性分数绘制在矩阵上：

* **低复杂性 (1-4)，低精确性 (1-4)**：简单 Crews
* **低复杂性 (1-4)，高精确性 (5-10)**：具有直接 LLM 调用的 Flows
* **高复杂性 (5-10)，低精确性 (1-4)**：复杂 Crews
* **高复杂性 (5-10)，高精确性 (5-10)**：协调 Crews 的 Flows

### 第 4 步：考虑其他因素

除了复杂性和精确性，还要考虑：

1. **开发时间**：Crews 通常原型制作更快
2. **维护需求**：Flows 提供更好的长期可维护性
3. **团队专业知识**：考虑您团队对不同方法的熟悉程度
4. **可扩展性需求**：Flows 通常对复杂应用具有更好的可扩展性
5. **集成需求**：考虑解决方案将如何与现有系统集成

## 结论

在 Crews 和 Flows 之间选择——或结合它们——是一个关键的架构决策，它会影响您 CrewAI 应用的有效性、可维护性和可扩展性。通过沿着复杂性和精确性的维度评估您的用例，您可以做出符合您特定要求的明智决策。

请记住，最佳方法通常会随着您应用的成熟而发展。从满足您的需求的最简单解决方案开始，并准备在获得经验且要求变得更加明确时优化您的架构。

<Check>
  您现在有了一个评估 CrewAI 用例的框架，并可以根据复杂性和精确性要求选择合适的方法。这将帮助您构建更有效、可维护和可扩展的 AI 应用。
</Check>

## 下一步

* 了解更多关于[制作有效的代理](/en/guides/agents/crafting-effective-agents)
* 探索[构建您的第一个 crew](/en/guides/crews/first-crew)
* 深入了解[掌握 flow 状态管理](/en/guides/flows/mastering-flow-state)
* 查看[核心概念](/en/concepts/agents)以获得更深入的理解