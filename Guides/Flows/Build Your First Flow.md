# 构建你的第一个流程

> 了解如何创建结构化的、事件驱动的工作流程，并对执行过程进行精确控制。

## 使用流程控制AI工作流

CrewAI流程代表了AI编排的下一级别 - 它将AI智能体团队的协作能力与过程化编程的精确性和灵活性相结合。虽然团队在智能体协作方面表现出色，但流程可以让你对AI系统的不同组件如何以及何时交互进行细粒度的控制。

在本指南中，我们将逐步创建一个强大的CrewAI流程，该流程可以生成任何主题的综合学习指南。本教程将演示流程如何通过结合常规代码、直接LLM调用和基于团队的处理来提供结构化的、事件驱动的工作流控制。

### 流程的强大之处

流程使你能够：

1. **结合不同的AI交互模式** - 使用团队处理复杂的协作任务，直接LLM调用处理简单操作，常规代码处理过程逻辑
2. **构建事件驱动系统** - 定义组件如何响应特定事件和数据变化
3. **在组件间维护状态** - 在应用程序的不同部分之间共享和转换数据
4. **与外部系统集成** - 将AI工作流与数据库、API和用户界面无缝连接
5. **创建复杂的执行路径** - 设计条件分支、并行处理和动态工作流

### 你将构建和学习的内容

完成本指南后，你将：

1. **创建一个复杂的内容生成系统**，结合用户输入、AI规划和多智能体内容创建
2. **协调系统不同组件之间的信息流**
3. **实现事件驱动架构**，其中每个步骤响应先前步骤的完成
4. **为更复杂的AI应用程序构建基础**，可以扩展和定制

这个指南创建器流程展示了可以应用于创建更高级应用程序的基本模式，例如：

* 结合多个专业子系统的交互式AI助手
* 具有AI增强转换的复杂数据处理管道
* 与外部服务和API集成的自主智能体
* 具有人类在环过程的多阶段决策系统

让我们开始构建你的第一个流程！

## 先决条件

在开始之前，请确保你已：

1. 按照[安装指南](/en/installation)安装CrewAI
2. 按照[LLM设置指南](/en/concepts/llms#setting-up-your-llm)在环境中设置LLM API密钥
3. 具备Python基础知识

## 第1步：创建新的CrewAI流程项目

首先，让我们使用CLI创建一个新的CrewAI流程项目。此命令将为你的流程设置一个包含所有必要目录和模板文件的脚手架项目。

```bash  theme={null}
crewai create flow guide_creator_flow
cd guide_creator_flow
```

这将生成一个具有流程所需基本结构的项目。

<Frame caption="CrewAI框架概览">
  <img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/flows.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=82ea168de2f004553dcea21410cd7d8a" alt="CrewAI Framework Overview" data-og-width="669" width="669" data-og-height="464" height="464" data-path="images/flows.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/flows.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=4a6177acae3789dd8e4467b791c8966e 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/flows.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=7900e4cdad93fd37bbcd2f1f2f38b40b 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/flows.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=a83fa78165e93bc1d988a30ebc33889a 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/flows.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=540eb3d8d8f256d6d703aa5e6111a4cd 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/flows.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=04fbb8e23728b87efa78a0a776e2d194 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/flows.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=ff06d73f5d4aa911154c66becf14d732 2500w" />
</Frame>

## 第2步：了解项目结构

生成的项目具有以下结构。花点时间熟悉它，因为了解此结构将帮助你在未来创建更复杂的流程。

```
guide_creator_flow/
├── .gitignore
├── pyproject.toml
├── README.md
├── .env
├── main.py
├── crews/
│   └── poem_crew/
│       ├── config/
│       │   ├── agents.yaml
│       │   └── tasks.yaml
│       └── poem_crew.py
└── tools/
    └── custom_tool.py
```

这种结构为流程的不同组件提供了清晰的分离：

* `main.py`文件中的主要流程逻辑
* `crews`目录中的专业团队
* `tools`目录中的自定义工具

我们将修改此结构来创建指南创建器流程，该流程将协调生成综合学习指南的过程。

## 第3步：添加内容编写团队

我们的流程需要一个专业团队来处理内容创建过程。让我们使用CrewAI CLI添加内容编写团队：

```bash  theme={null}
crewai flow add-crew content-crew
```

此命令会自动为你的团队创建必要的目录和模板文件。内容编写团队将负责编写和审阅我们指南的部分内容，在我们主应用程序编排的整体流程中工作。

## 第4步：配置内容编写团队

现在，让我们修改内容编写团队的生成文件。我们将设置两个专业智能体 - 一个编写者和一个审阅者 - 他们将协作创建我们指南的高质量内容。

1. 首先，更新代理配置文件以定义我们的内容创建团队：

   记得将`llm`设置为你正在使用的提供商。

```yaml  theme={null}
# src/guide_creator_flow/crews/content_crew/config/agents.yaml
content_writer:
  role: >
    教育内容编写者
  goal: >
    创建引人入胜、信息丰富的内容，彻底解释指定主题
    并为读者提供有价值的见解
  backstory: >
    你是一位才华横溢的教育作家，擅长创建清晰、引人入胜的内容。
    你有以易于理解的语言解释复杂概念并组织信息以帮助读者构建理解的才能。
  llm: provider/model-id  # 例如 openai/gpt-4o, google/gemini-2.0-flash, anthropic/claude...

content_reviewer:
  role: >
    教育内容审阅者和编辑
  goal: >
    确保内容准确、全面、结构良好，并与先前编写的部分保持一致性
  backstory: >
    你是一位一丝不苟的编辑，拥有多年的教育内容审阅经验。
    你注重细节、清晰性和连贯性。你擅长在保持原作者声音的同时改进内容，
    并确保多个部分之间的一致质量。
  llm: provider/model-id  # 例如 openai/gpt-4o, google/gemini-2.0-flash, anthropic/claude...
```

这些智能体定义了塑造AI智能体处理内容创建方式的专门角色和视角。注意每个智能体如何具有不同的目的和专业能力。

2. 接下来，更新任务配置文件以定义具体的编写和审阅任务：

```yaml  theme={null}
# src/guide_creator_flow/crews/content_crew/config/tasks.yaml
write_section_task:
  description: >
    编写关于主题"{section_title}"的全面部分

    部分描述：{section_description}
    目标受众：{audience_level}水平学习者

    你的内容应该：
    1. 以对该部分主题的简要介绍开始
    2. 用例子清楚地解释所有关键概念
    3. 在适当的地方包括实际应用或练习
    4. 以关键点总结结束
    5. 长度约为500-800字

    使用Markdown格式化你的内容，包括适当的标题、列表和强调。

    先前编写的部分：
    {previous_sections}

    确保你的内容与先前编写的部分保持一致性，
    并基于已经解释的概念进行构建。
  expected_output: >
    一个结构良好、全面的Markdown格式部分，彻底解释主题并适合目标受众。
  agent: content_writer

review_section_task:
  description: >
    审阅并改进以下关于"{section_title}"的部分：

    {draft_content}

    目标受众：{audience_level}水平学习者

    先前编写的部分：
    {previous_sections}

    你的审阅应该：
    1. 修复任何语法或拼写错误
    2. 提高清晰度和可读性
    3. 确保内容全面且准确
    4. 验证与先前编写部分的一致性
    5. 增强结构和流程
    6. 添加任何遗漏的关键信息

    以Markdown格式提供改进后的部分版本。
  expected_output: >
    部分的改进、精炼版本，保持原始结构但增强清晰度、准确性和一致性。
  agent: content_reviewer
  context:
    - write_section_task
```

这些任务定义为我们的智能体提供了详细说明，确保他们生产符合我们质量标准的内容。注意审阅任务中的`context`参数如何创建一个工作流，使审阅者能够访问编写者的输出。

3. 现在，更新团队实现文件以定义我们的智能体和任务如何协同工作：

```python  theme={null}
# src/guide_creator_flow/crews/content_crew/content_crew.py
from crewai import Agent, Crew, Process, Task
from crewai.project import CrewBase, agent, crew, task
from crewai.agents.agent_builder.base_agent import BaseAgent
from typing import List

@CrewBase
class ContentCrew():
    """内容编写团队"""

    agents: List[BaseAgent]
    tasks: List[Task]

    @agent
    def content_writer(self) -> Agent:
        return Agent(
            config=self.agents_config['content_writer'], # type: ignore[index]
            verbose=True
        )

    @agent
    def content_reviewer(self) -> Agent:
        return Agent(
            config=self.agents_config['content_reviewer'], # type: ignore[index]
            verbose=True
        )

    @task
    def write_section_task(self) -> Task:
        return Task(
            config=self.tasks_config['write_section_task'] # type: ignore[index]
        )

    @task
    def review_section_task(self) -> Task:
        return Task(
            config=self.tasks_config['review_section_task'], # type: ignore[index]
            context=[self.write_section_task()]
        )

    @crew
    def crew(self) -> Crew:
        """创建内容编写团队"""
        return Crew(
            agents=self.agents,
            tasks=self.tasks,
            process=Process.sequential,
            verbose=True,
        )
```

这个团队定义建立了我们的智能体和任务之间的关系，设置了一个顺序流程，其中内容编写者创建草稿，然后审阅者改进它。虽然这个团队可以独立运行，但在我们的流程中，它将作为更大系统的一部分进行编排。

## 第5步：创建流程

现在到了令人兴奋的部分 - 创建将协调整个指南创建过程的流程。在这里，我们将常规Python代码、直接LLM调用和内容创建团队组合成一个连贯的系统。

我们的流程将：

1. 获取用户输入的主题和受众水平
2. 进行直接LLM调用来创建结构化的指南大纲
3. 使用内容编写团队按顺序处理每个部分
4. 将所有内容组合成最终的综合文档

让我们在`main.py`文件中创建我们的流程：

```python  theme={null}
#!/usr/bin/env python
import json
import os
from typing import List, Dict
from pydantic import BaseModel, Field
from crewai import LLM
from crewai.flow.flow import Flow, listen, start
from guide_creator_flow.crews.content_crew.content_crew import ContentCrew

# 定义我们的结构化数据模型
class Section(BaseModel):
    title: str = Field(description="部分的标题")
    description: str = Field(description="部分应涵盖内容的简要描述")

class GuideOutline(BaseModel):
    title: str = Field(description="指南的标题")
    introduction: str = Field(description="主题的介绍")
    target_audience: str = Field(description="目标受众的描述")
    sections: List[Section] = Field(description="指南中的部分列表")
    conclusion: str = Field(description="指南的结论或总结")

# 定义我们的流程状态
class GuideCreatorState(BaseModel):
    topic: str = ""
    audience_level: str = ""
    guide_outline: GuideOutline = None
    sections_content: Dict[str, str] = {}

class GuideCreatorFlow(Flow[GuideCreatorState]):
    """用于创建任何主题综合指南的流程"""

    @start()
    def get_user_input(self):
        """获取用户关于指南主题和受众的输入"""
        print("\n=== 创建你的综合指南 ===\n")

        # 获取用户输入
        self.state.topic = input("你想创建什么主题的指南？ ")

        # 获取受众水平并进行验证
        while True:
            audience = input("你的目标受众是谁？(beginner/intermediate/advanced) ").lower()
            if audience in ["beginner", "intermediate", "advanced"]:
                self.state.audience_level = audience
                break
            print("请输入'beginner'、'intermediate'或'advanced'")

        print(f"\n为{self.state.audience_level}受众创建关于{self.state.topic}的指南...\n")
        return self.state

    @listen(get_user_input)
    def create_guide_outline(self, state):
        """使用直接LLM调用为指南创建结构化大纲"""
        print("创建指南大纲...")

        # 初始化LLM
        llm = LLM(model="openai/gpt-4o-mini", response_format=GuideOutline)

        # 创建大纲的消息
        messages = [
            {"role": "system", "content": "你是一个设计用于输出JSON的有用助手。"},
            {"role": "user", "content": f"""
            为"{state.topic}"主题为{state.audience_level}水平学习者创建一个详细的指南大纲。

            大纲应包括：
            1. 一个引人注目的指南标题
            2. 主题介绍
            3. 4-6个涵盖主题最重要方面的主要部分
            4. 结论或总结

            对于每个部分，提供清晰的标题和简要的应涵盖内容描述。
            """}
        ]

        # 进行LLM调用以获取JSON响应格式
        response = llm.call(messages=messages)

        # 解析JSON响应
        outline_dict = json.loads(response)
        self.state.guide_outline = GuideOutline(**outline_dict)

        # 确保在保存之前输出目录存在
        os.makedirs("output", exist_ok=True)

        # 将大纲保存到文件
        with open("output/guide_outline.json", "w") as f:
            json.dump(outline_dict, f, indent=2)

        print(f"指南大纲已创建，包含{len(self.state.guide_outline.sections)}个部分")
        return self.state.guide_outline

    @listen(create_guide_outline)
    def write_and_compile_guide(self, outline):
        """编写所有部分并编译指南"""
        print("编写指南部分并编译...")
        completed_sections = []

        # 逐个处理部分以保持上下文流程
        for section in outline.sections:
            print(f"处理部分：{section.title}")

            # 从先前部分构建上下文
            previous_sections_text = ""
            if completed_sections:
                previous_sections_text = "# 先前编写的部分\n\n"
                for title in completed_sections:
                    previous_sections_text += f"## {title}\n\n"
                    previous_sections_text += self.state.sections_content.get(title, "") + "\n\n"
            else:
                previous_sections_text = "尚未编写先前部分。"

            # 为此部分运行内容团队
            result = ContentCrew().crew().kickoff(inputs={
                "section_title": section.title,
                "section_description": section.description,
                "audience_level": self.state.audience_level,
                "previous_sections": previous_sections_text,
                "draft_content": ""
            })

            # 存储内容
            self.state.sections_content[section.title] = result.raw
            completed_sections.append(section.title)
            print(f"部分已完成：{section.title}")

        # 编译最终指南
        guide_content = f"# {outline.title}\n\n"
        guide_content += f"## 介绍\n\n{outline.introduction}\n\n"

        # 按顺序添加每个部分
        for section in outline.sections:
            section_content = self.state.sections_content.get(section.title, "")
            guide_content += f"\n\n{section_content}\n\n"

        # 添加结论
        guide_content += f"## 结论\n\n{outline.conclusion}\n\n"

        # 保存指南
        with open("output/complete_guide.md", "w") as f:
            f.write(guide_content)

        print("\n综合指南已编译并保存到output/complete_guide.md")
        return "指南创建成功完成"

def kickoff():
    """运行指南创建器流程"""
    GuideCreatorFlow().kickoff()
    print("\n=== 流程完成 ===")
    print("你的综合指南已在output目录中准备就绪。")
    print("打开output/complete_guide.md查看它。")

def plot():
    """生成流程的可视化"""
    flow = GuideCreatorFlow()
    flow.plot("guide_creator_flow")
    print("流程可视化已保存到guide_creator_flow.html")

if __name__ == "__main__":
    kickoff()
```

让我们分析这个流程中发生的情况：

1. 我们定义Pydantic模型用于结构化数据，确保类型安全和清晰的数据表示
2. 我们创建一个状态类以在流程的不同步骤之间维护数据
3. 我们实现三个主要流程步骤：
   * 使用`@start()`装饰器获取用户输入
   * 使用直接LLM调用创建指南大纲
   * 使用内容团队处理部分
4. 我们使用`@listen()`装饰器建立步骤之间的事件驱动关系

这就是流程的力量 - 将不同类型的处理（用户交互、直接LLM调用、基于团队的任务）组合成一个连贯的、事件驱动的系统。

## 第6步：设置环境变量

在项目根目录创建一个`.env`文件，包含你的API密钥。有关配置提供商的详细信息，请参阅[LLM设置指南](/en/concepts/llms#setting-up-your-llm)。

```sh .env theme={null}
OPENAI_API_KEY=your_openai_api_key
# 或
GEMINI_API_KEY=your_gemini_api_key
# 或
ANTHROPIC_API_KEY=your_anthropic_api_key
```

## 第7步：安装依赖项

安装所需的依赖项：

```bash  theme={null}
crewai install
```

## 第8步：运行你的流程

现在是时候看到你的流程生效了！使用CrewAI CLI运行它：

```bash  theme={null}
crewai flow kickoff
```

当你运行此命令时，你将看到你的流程开始运行：

1. 它将提示你输入主题和受众水平
2. 它将为你的指南创建结构化大纲
3. 它将处理每个部分，内容编写者和审阅者协作处理每个部分
4. 最后，它将所有内容编译成一个综合指南

这展示了流程编排涉及多个组件（AI和非AI）的复杂过程的能力。

## 第9步：可视化你的流程

流程的强大功能之一是能够可视化其结构：

```bash  theme={null}
crewai flow plot
```

这将创建一个HTML文件，显示流程的结构，包括不同步骤之间的关系以及它们之间流动的数据。这种可视化对于理解和调试复杂流程非常宝贵。

## 第10步：审查输出

流程完成后，你将在`output`目录中找到两个文件：

1. `guide_outline.json`：包含指南的结构化大纲
2. `complete_guide.md`：包含所有部分的综合指南

花点时间审查这些文件，欣赏你构建的内容 - 一个结合用户输入、直接AI交互和协作智能体工作以产生复杂、高质量输出的系统。

## 可能的艺术：超越你的第一个流程

你在本指南中学到的内容为创建更复杂的AI系统提供了基础。以下是扩展这个基本流程的一些方法：

### 增强用户交互

你可以创建更具交互性的流程：

* 用于输入和输出的Web界面
* 实时进度更新
* 交互式反馈和优化循环
* 多阶段用户交互

### 添加更多处理步骤

你可以使用其他步骤扩展流程：

* 大纲创建之前的研究
* 插图的图像生成
* 技术指南的代码片段生成
* 最终质量保证和事实检查

### 创建更复杂的流程

你可以实现更复杂的流程模式：

* 基于用户偏好或内容类型的条件分支
* 独立部分的并行处理
* 带有反馈的迭代优化循环
* 与外部API和服务的集成

### 应用于不同领域

相同的模式可用于创建以下流程：

* **交互式故事讲述**：基于用户输入创建个性化故事
* **商业智能**：处理数据、生成见解和创建报告
* **产品开发**：促进构思、设计和规划
* **教育系统**：创建个性化学习体验

## 演示的关键功能

这个指南创建器流程展示了CrewAI的几个强大功能：

1. **用户交互**：流程直接从用户收集输入
2. **直接LLM调用**：使用LLM类进行高效、单一用途的AI交互
3. **Pydantic结构化数据**：使用Pydantic模型确保类型安全
4. **带上下文的顺序处理**：按顺序编写部分，提供先前部分作为上下文
5. **多智能体团队**：利用专业智能体（编写者和审阅者）进行内容创建
6. **状态管理**：在整个过程的不同步骤之间维护状态
7. **事件驱动架构**：使用`@listen`装饰器响应事件

## 理解流程结构

让我们分解流程的关键组件，以帮助你了解如何构建自己的流程：

### 1. 直接LLM调用

流程允许你直接调用语言模型，当你需要简单的、结构化的响应时：

```python  theme={null}
llm = LLM(
    model="model-id-here",  # gpt-4o, gemini-2.0-flash, anthropic/claude...
    response_format=GuideOutline
)
response = llm.call(messages=messages)
```

当你需要特定的、结构化的输出时，这比使用团队更高效。

### 2. 事件驱动架构

流程使用装饰器建立组件之间的关系：

```python  theme={null}
@start()
def get_user_input(self):
    # 流程中的第一步
    # ...

@listen(get_user_input)
def create_guide_outline(self, state):
    # 当get_user_input完成时运行
    # ...
```

这为你的应用程序创建了清晰的、声明性的结构。

### 3. 状态管理

流程在步骤之间维护状态，使共享数据变得容易：

```python  theme={null}
class GuideCreatorState(BaseModel):
    topic: str = ""
    audience_level: str = ""
    guide_outline: GuideOutline = None
    sections_content: Dict[str, str] = {}
```

这提供了类型安全的方式来在整个流程中跟踪和转换数据。

### 4. 团队集成

流程可以与团队无缝集成以处理复杂的协作任务：

```python  theme={null}
result = ContentCrew().crew().kickoff(inputs={
    "section_title": section.title,
    # ...
})
```

这允许你为应用程序的每个部分使用正确的工具 - 直接LLM调用处理简单任务，团队处理复杂协作。

## 下一步

现在你已经构建了第一个流程，你可以：

1. 尝试更复杂的流程结构和模式
2. 尝试使用`@router()`在流程中创建条件分支
3. 探索`and_`和`or_`函数以实现更复杂的并行执行
4. 将你的流程连接到外部API、数据库或用户界面
5. 在单个流程中组合多个专业团队

<Check>
  恭喜！你已成功构建了第一个CrewAI流程，该流程结合常规代码、直接LLM调用和基于团队的处理来创建综合指南。这些基础技能使你能够创建越来越复杂的AI应用程序，通过过程控制和协作智能的结合来解决复杂的多阶段问题。
</Check>