# 自定义管理代理

> 了解如何在CrewAI中设置自定义代理作为管理器，从而对任务管理和协调提供更多控制。

# 在CrewAI中设置特定代理作为管理器

CrewAI允许用户设置一个特定代理作为团队的管理器，从而对任务的管理和协调提供更多控制。
这一功能使您能够定制管理角色，以更好地适应您的项目需求。

## 使用`manager_agent`属性

### 自定义管理代理

`manager_agent`属性允许您定义一个自定义代理来管理团队。该代理将监督整个过程，确保任务高效完成并达到最高标准。

### 示例

```python
import os
from crewai import Agent, Task, Crew, Process

# 定义您的代理
researcher = Agent(
    role="研究员",
    goal="对AI和AI代理进行全面的研究和分析",
    backstory="您是一名专业研究员，专攻技术、软件工程、AI和初创企业。您目前是一名自由职业者，正在为新客户进行研究。",
    allow_delegation=False,
)

writer = Agent(
    role="高级撰稿人",
    goal="创建关于AI和AI代理的有吸引力的内容",
    backstory="您是一名高级撰稿人，专攻技术、软件工程、AI和初创企业。您目前是一名自由职业者，正在为新客户撰写内容。",
    allow_delegation=False,
)

# 定义您的任务
task = Task(
    description="生成5个文章创意列表，然后为每个创意写一段引人入胜的段落，展示该主题完整文章的潜力。返回包含段落和注释的创意列表。",
    expected_output="5个要点，每个要点包含一个段落和相应的注释。",
)

# 定义管理器代理
manager = Agent(
    role="项目经理",
    goal="高效管理团队并确保高质量完成任务",
    backstory="您是一名经验丰富的项目经理，擅长监督复杂项目并引导团队取得成功。您的角色是协调团队成员的努力，确保每项任务按时完成并达到最高标准。",
    allow_delegation=True,
)

# 使用自定义管理器实例化您的团队
crew = Crew(
    agents=[researcher, writer],
    tasks=[task],
    manager_agent=manager,
    process=Process.hierarchical,
)

# 启动团队的工作
result = crew.kickoff()
```

## 自定义管理代理的优势

* **增强控制**：根据项目的具体需求定制管理方法。
* **改善协调**：通过经验丰富的代理确保有效的任务协调和管理。
* **可定制管理**：定义与项目目标一致的管理角色和职责。

## 设置管理器LLM

如果您使用的是分层流程但又不想设置自定义管理代理，您可以指定管理器的语言模型：

```python
from crewai import LLM

manager_llm = LLM(model="gpt-4o")

crew = Crew(
    agents=[researcher, writer],
    tasks=[task],
    process=Process.hierarchical,
    manager_llm=manager_llm
)
```

<Note>
  使用分层流程时，必须设置`manager_agent`或`manager_llm`中的一个。
</Note>