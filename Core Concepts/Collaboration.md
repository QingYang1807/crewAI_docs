# 协作

> 如何使Agent在CrewAI团队中协同工作、委派任务并有效沟通。

## 概述

CrewAI中的协作使Agent能够通过委派任务和提问来利用彼此的专业知识，像团队一样协同工作。当设置`allow_delegation=True`时，Agent会自动获得强大的协作工具。

## 快速入门：启用协作

```python
from crewai import Agent, Crew, Task

# 为Agent启用协作
researcher = Agent(
    role="研究专员",
    goal="对任何主题进行全面研究",
    backstory="能够接触各种来源的专业研究员",
    allow_delegation=True,  # 🔑 协作的关键设置
    verbose=True
)

writer = Agent(
    role="内容撰写人", 
    goal="基于研究创建引人入胜的内容",
    backstory="将研究转化为引人入胜内容的熟练写手",
    allow_delegation=True,  # 🔑 允许向其他Agent提问
    verbose=True
)

# Agent现在可以自动协作
crew = Crew(
    agents=[researcher, writer],
    tasks=[...],
    verbose=True
)
```

## Agent协作的工作原理

当设置`allow_delegation=True`时，CrewAI自动为Agent提供两个强大的工具：

### 1. **委派工作工具**

允许Agent将任务分配给具有特定专业知识的团队成员。

```python
# Agent自动获得此工具：
# Delegate work to coworker(task: str, context: str, coworker: str)
```

### 2. **提问工具**

使Agent能够提出具体问题，从同事处收集信息。

```python
# Agent自动获得此工具：
# Ask question to coworker(question: str, context: str, coworker: str)
```

## 协作实践

这是一个展示Agent在内容创作任务上协作的完整示例：

```python
from crewai import Agent, Crew, Task, Process

# 创建协作Agent
researcher = Agent(
    role="研究专员",
    goal="查找任何主题的准确、最新信息",
    backstory="""你是一个细致的研究员，擅长在各个领域
    找到可靠来源并核实信息。""",
    allow_delegation=True,
    verbose=True
)

writer = Agent(
    role="内容撰写人",
    goal="创建引人入胜、结构良好的内容",
    backstory="""你是一个熟练的内容撰写人，擅长将研究
    转化为针对不同受众的引人入胜、可读性强的内容。""",
    allow_delegation=True,
    verbose=True
)

editor = Agent(
    role="内容编辑",
    goal="确保内容质量和一致性",
    backstory="""你是一位经验丰富的编辑，注重细节，
    确保内容达到清晰度和准确性方面的高标准。""",
    allow_delegation=True,
    verbose=True
)

# 创建一个鼓励协作的任务
article_task = Task(
    description="""撰写一篇1000字的关于"人工智能在医疗保健中的未来"的综合文章。
  
    文章应包括：
    - 人工智能在医疗保健中的当前应用
    - 新兴趋势和技术
    - 潜在挑战和伦理考量
    - 未来5年的专家预测
  
    与团队成员协作以确保准确性和质量。""",
    expected_output="一篇研究充分、引人入胜的1000字文章，具有适当的结构和引用",
    agent=writer  # 撰写人主导，但可以将研究委派给研究专员
)

# 创建协作团队
crew = Crew(
    agents=[researcher, writer, editor],
    tasks=[article_task],
    process=Process.sequential,
    verbose=True
)

result = crew.kickoff()
```

## 协作模式

### 模式1：研究 → 撰写 → 编辑

```python
research_task = Task(
    description="研究量子计算的最新发展",
    expected_output="包含关键发现和来源的综合研究摘要",
    agent=researcher
)

writing_task = Task(
    description="基于研究发现撰写文章",
    expected_output="一篇关于量子计算的引人入胜的800字文章",
    agent=writer,
    context=[research_task]  # 获取研究结果作为上下文
)

editing_task = Task(
    description="编辑并润色文章以供发布",
    expected_output="经过改进清晰度和流畅度的可供发布的文章",
    agent=editor,
    context=[writing_task]  # 获取文章草稿作为上下文
)
```

### 模式2：单一任务协作

```python
collaborative_task = Task(
    description="""为新的AI产品创建营销策略。
  
    撰写人：专注于信息和内容策略
    研究专员：提供市场分析和竞争对手洞察
  
    共同协作创建全面策略。""",
    expected_output="具有研究支持的综合营销策略",
    agent=writer  # 主导Agent，但可以委派给研究专员
)
```

## 层次协作

对于复杂项目，使用具有管理Agent的层次化流程：

```python
from crewai import Agent, Crew, Task, Process

# 管理Agent协调团队
manager = Agent(
    role="项目经理",
    goal="协调团队工作并确保项目成功",
    backstory="经验丰富的项目经理，擅长委派和质量控制",
    allow_delegation=True,
    verbose=True
)

# 专业Agent
researcher = Agent(
    role="研究员",
    goal="提供准确的研究和分析",
    backstory="具有深厚分析技能的专业研究员",
    allow_delegation=False,  # 专业人员专注于其专业领域
    verbose=True
)

writer = Agent(
    role="撰写人", 
    goal="创建引人入胜的内容",
    backstory="创建引人入胜内容的熟练写手",
    allow_delegation=False,
    verbose=True
)

# 管理主导的任务
project_task = Task(
    description="创建包含建议的综合市场分析报告",
    expected_output="执行摘要、详细分析和战略建议",
    agent=manager  # 经理将委派给专业人员
)

# 层次化团队
crew = Crew(
    agents=[manager, researcher, writer],
    tasks=[project_task],
    process=Process.hierarchical,  # 经理协调一切
    manager_llm="gpt-4o",  # 指定经理使用的LLM
    verbose=True
)
```

## 协作最佳实践

### 1. **明确的角色定义**

```python
# ✅ 良好：具体、互补的角色
researcher = Agent(role="市场研究分析师", ...)
writer = Agent(role="技术内容撰写人", ...)

# ❌ 避免：重叠或模糊的角色
agent1 = Agent(role="通用助手", ...)
agent2 = Agent(role="辅助员", ...)
```

### 2. **战略性委派启用**

```python
# ✅ 为协调员和通才启用委派
lead_agent = Agent(
    role="内容负责人",
    allow_delegation=True,  # 可以委派给专业人员
    ...
)

# ✅ 为专注的专业人员禁用（可选）
specialist_agent = Agent(
    role="数据分析师", 
    allow_delegation=False,  # 专注于核心专业
    ...
)
```

### 3. **上下文共享**

```python
# ✅ 使用上下文参数处理任务依赖
writing_task = Task(
    description="基于研究撰写文章",
    agent=writer,
    context=[research_task],  # 共享研究结果
    ...
)
```

### 4. **明确的任务描述**

```python
# ✅ 具体、可操作的描述
Task(
    description="""研究AI聊天机器人领域的竞争对手。
    重点关注：定价模式、关键功能、目标市场。
    以结构化格式提供数据。""",
    ...
)

# ❌ 不指导协作的模糊描述
Task(description="对聊天机器人进行一些研究", ...)
```

## 协作故障排除

### 问题：Agent不协作

**症状：Agent孤立工作，没有委派发生

```python
# ✅ 解决方案：确保启用委派
agent = Agent(
    role="...",
    allow_delegation=True,  # 这是必需的！
    ...
)
```

### 问题：过多的来回沟通

**症状：Agent问过多问题，进展缓慢

```python
# ✅ 解决方案：提供更好的上下文和具体角色
Task(
    description="""撰写一篇关于机器学习的技术博客文章。
  
    上下文：目标受众是具有基础ML知识的软件开发人员。
    长度：1200字
    包括：代码示例、实际应用、最佳实践
  
    如果需要特定技术细节，将研究委派给研究专员。""",
    ...
)
```

### 问题：委派循环

**症状：Agent无限期地来回委派

```python
# ✅ 解决方案：明确的层次和责任
manager = Agent(role="经理", allow_delegation=True)
specialist1 = Agent(role="专业人员A", allow_delegation=False)  # 不能重新委派
specialist2 = Agent(role="专业人员B", allow_delegation=False)
```

## 高级协作功能

### 自定义协作规则

```python
# 在Agent背景故事中设置特定的协作指南
agent = Agent(
    role="高级开发人员",
    backstory="""你领导开发项目并与团队成员协调。
  
    协作指南：
    - 将研究任务委派给研究分析师
    - 向设计师寻求UI/UX指导
    - 咨询质量保证工程师的测试策略
    - 只将阻碍问题上报给项目经理""",
    allow_delegation=True
)
```

### 监控协作

```python
def track_collaboration(output):
    """跟踪协作模式"""
    if "Delegate work to coworker" in output.raw:
        print("🤝 发生了委派")
    if "Ask question to coworker" in output.raw:
        print("❓ 提出了问题")

crew = Crew(
    agents=[...],
    tasks=[...],
    step_callback=track_collaboration,  # 监控协作
    verbose=True
)
```

## 记忆和学习

使Agent能够记住过去的协作：

```python
agent = Agent(
    role="内容负责人",
    memory=True,  # 记住过去的交互
    allow_delegation=True,
    verbose=True
)
```

启用记忆功能后，Agent会从过去的协作中学习，并随时间改进其委派决策。

## 下一步

* **尝试示例**：从基本协作示例开始
* **尝试不同角色**：测试不同的Agent角色组合
* **监控交互**：使用`verbose=True`查看协作过程
* **优化任务描述**：清晰的任务带来更好的协作
* **扩展规模**：尝试复杂项目的层次化流程

协作将单个AIAgent转变为能够共同应对复杂、多方面挑战的强大团队。