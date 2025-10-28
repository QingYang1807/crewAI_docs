# 构建你的第一个Crew团队

> 逐步教程，创建一个协作式AI团队，共同解决复杂问题。

## 释放协作AI的力量

想象一下，拥有一支专业的AI代理团队无缝协作解决复杂问题，每个代理都贡献其独特的技能来实现共同目标。这就是CrewAI的力量 - 一个使你能够创建协作式AI系统的框架，这些系统可以完成单个AI无法实现的任务。

在本指南中，我们将逐步创建一个研究团队，帮助我们研究和分析一个主题，然后创建一份全面的报告。这个实际例子展示了AI代理如何协作完成复杂任务，但这仅仅是CrewAI可能性的开始。

### 你将构建和学习的内容

完成本指南后，你将：

1. **创建一个专业的AI研究团队**，具有不同的角色和职责
2. **编排多个AI代理之间的协作**
3. **自动化一个复杂的工作流程**，包括信息收集、分析和报告生成
4. **构建基础技能**，可以应用于更有雄心的项目

虽然我们在本指南中构建的是一个简单的研究团队，但相同的模式和技术可以应用于创建更复杂的团队，用于以下任务：

* 多阶段内容创作，包括专业写手、编辑和事实核查员
* 复杂的客户服务系统，具有分层支持代理
* 自主业务分析师，收集数据、创建可视化并生成洞察
* 产品开发团队，进行构思、设计和规划实施

让我们开始构建你的第一个团队吧！

### 先决条件

在开始之前，请确保你已：

1. 按照[安装指南](/en/installation)安装CrewAI
2. 按照LLM设置指南在你的环境中设置LLM API密钥[/en/concepts/llms#setting-up-your-llm]
3. 基本了解Python

## 步骤1：创建新的CrewAI项目

首先，让我们使用CLI创建一个新的CrewAI项目。这个命令将设置一个包含所有必要文件的完整项目结构，使你可以专注于定义代理和任务，而不是设置样板代码。

```bash  theme={null}
crewai create crew research_crew
cd research_crew
```

这将生成一个具有团队所需基本结构的项目。CLI自动创建：

* 包含必要文件的项目目录
* 代理和任务的配置文件
* 基本的团队实现
* 运行团队的主脚本

<Frame caption="CrewAI框架概览">
  <img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crews.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=514fd0b06e4128e62f10728d44601975" alt="CrewAI框架概览" data-og-width="634" width="634" data-og-height="473" height="473" data-path="images/crews.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crews.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=279c5c26c77fc9acc8411677716fa5ee 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crews.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=92b76be34b84b36771e0a8eed8976966 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crews.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=3ef573e6215967af1bb2975a58d0d859 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crews.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=1af6e6a337b70ca2ce238d8e40f49218 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crews.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=c5da01705f1373446f8582ac582ff244 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crews.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=96464aab7bb5efe4213a7b80f58038aa 2500w" />
</Frame>

## 步骤2：探索项目结构

让我们花点时间了解CLI创建的项目结构。CrewAI遵循Python项目的最佳实践，使你能够在团队变得更加复杂时轻松维护和扩展代码。

```
research_crew/
├── .gitignore
├── pyproject.toml
├── README.md
├── .env
└── src/
    └── research_crew/
        ├── __init__.py
        ├── main.py
        ├── crew.py
        ├── tools/
        │   ├── custom_tool.py
        │   └── __init__.py
        └── config/
            ├── agents.yaml
            └── tasks.yaml
```

这个结构遵循Python项目的最佳实践，使你能够轻松组织代码。配置文件（YAML格式）与实现代码（Python格式）的分离使你可以轻松修改团队的行为而无需更改底层代码。

## 步骤3：配置你的代理

现在到了有趣的部分 - 定义你的AI代理！在CrewAI中，代理是具有特定角色、目标和背景故事的专业实体，这些因素塑造了它们的行为。将它们想象成戏剧中的角色，每个角色都有自己的个性和目的。

对于我们的研究团队，我们将创建两个代理：

1. 一个**研究员**，擅长查找和组织信息
2. 一个**分析师**，可以解释研究结果并创建有洞察力的报告

让我们修改`agents.yaml`文件来定义这些专业代理。确保将`llm`设置为你正在使用的提供商。

```yaml  theme={null}
# src/research_crew/config/agents.yaml
researcher:
  role: >
    {topic}的高级研究专家
  goal: >
    找到关于{topic}的全面准确的信息，
    重点关注最新发展和关键见解
  backstory: >
    你是一位经验丰富的研究专家，擅长从各种来源找到相关信息。
    你擅长以清晰和结构化的方式组织信息，使复杂的主题对其他人来说易于理解。
  llm: provider/model-id  # 例如 openai/gpt-4o, google/gemini-2.0-flash, anthropic/claude...

analyst:
  role: >
    {topic}的数据分析师和报告撰写人
  goal: >
    分析研究结果并创建一份全面的、结构良好的报告，
    以清晰和引人入胜的方式呈现见解
  backstory: >
    你是一位技能娴熟的分析师，具有数据解释和技术写作背景。
    你擅长从研究数据中识别模式和提取有意义的见解，然后通过精心制作的报告有效地传达这些见解。
  llm: provider/model-id  # 例如 openai/gpt-4o, google/gemini-2.0-flash, anthropic/claude...
```

注意每个代理都有不同的角色、目标和背景故事。这些元素不仅仅是描述性的 - 它们积极地塑造了代理处理任务的方式。通过精心设计这些元素，你可以创建具有互补专业技能和视角的代理。

## 步骤4：定义你的任务

定义了代理后，我们现在需要给它们具体的任务来执行。在CrewAI中，任务代表代理将执行的具体工作，包含详细的指导和预期输出。

对于我们的研究团队，我们将定义两个主要任务：

1. 一个**研究任务**，用于收集全面的信息
2. 一个**分析任务**，用于创建有洞察力的报告

让我们修改`tasks.yaml`文件：

```yaml  theme={null}
# src/research_crew/config/tasks.yaml
research_task:
  description: >
    对{topic}进行彻底研究。重点关注：
    1. 关键概念和定义
    2. 历史发展和近期趋势
    3. 主要挑战和机遇
    4. 值得注意的应用或案例研究
    5. 未来前景和潜在发展

    确保以结构化的格式组织你的发现，包含清晰的章节。
  expected_output: >
    一份全面的研究文档，包含结构良好的章节，涵盖{topic}的所有要求方面。
    在相关处包括具体事实、数据和示例。
  agent: researcher

analysis_task:
  description: >
    分析研究结果并创建关于{topic}的综合报告。
    你的报告应该：
    1. 以执行摘要开始
    2. 包括研究中的所有关键信息
    3. 提供对趋势和模式的深入分析
    4. 提供建议或未来考虑事项
    5. 以专业、易读的风格格式化，包含清晰的标题
  expected_output: >
    一份关于{topic}的精致、专业的报告，呈现研究结果并添加分析和见解。
    报告应该结构良好，包含执行摘要、主要章节和结论。
  agent: analyst
  context:
    - research_task
  output_file: output/report.md
```

注意分析任务中的`context`字段 - 这是一个强大的功能，允许分析师访问研究任务的输出。这创建了一个信息在代理之间自然流动的工作流程，就像在人类团队中一样。

## 步骤5：配置你的团队

现在是通过配置我们的团队将所有内容整合在一起的时候了。团队是编排代理如何协作完成任务的容器。

让我们修改`crew.py`文件：

```python  theme={null}
# src/research_crew/crew.py
from crewai import Agent, Crew, Process, Task
from crewai.project import CrewBase, agent, crew, task
from crewai_tools import SerperDevTool
from crewai.agents.agent_builder.base_agent import BaseAgent
from typing import List

@CrewBase
class ResearchCrew():
    """用于全面主题分析和报告的研究团队"""

    agents: List[BaseAgent]
    tasks: List[Task]

    @agent
    def researcher(self) -> Agent:
        return Agent(
            config=self.agents_config['researcher'], # type: ignore[index]
            verbose=True,
            tools=[SerperDevTool()]
        )

    @agent
    def analyst(self) -> Agent:
        return Agent(
            config=self.agents_config['analyst'], # type: ignore[index]
            verbose=True
        )

    @task
    def research_task(self) -> Task:
        return Task(
            config=self.tasks_config['research_task'] # type: ignore[index]
        )

    @task
    def analysis_task(self) -> Task:
        return Task(
            config=self.tasks_config['analysis_task'], # type: ignore[index]
            output_file='output/report.md'
        )

    @crew
    def crew(self) -> Crew:
        """创建研究团队"""
        return Crew(
            agents=self.agents,
            tasks=self.tasks,
            process=Process.sequential,
            verbose=True,
        )
```

在这段代码中，我们正在：

1. 创建研究员代理并为其配备SerperDevTool以搜索网络
2. 创建分析师代理
3. 设置研究和分析任务
4. 配置团队按顺序运行任务（分析师将等待研究员完成）

这就是魔法发生的地方 - 仅用几行代码，我们就定义了一个协作式AI系统，其中专业代理在协调过程中一起工作。

## 步骤6：设置你的主脚本

现在，让我们设置将运行我们团队的主脚本。这是我们在这里提供希望团队研究的特定主题的地方。

```python  theme={null}
#!/usr/bin/env python
# src/research_crew/main.py
import os
from research_crew.crew import ResearchCrew

# 如果不存在则创建输出目录
os.makedirs('output', exist_ok=True)

def run():
    """
    运行研究团队。
    """
    inputs = {
        'topic': '医疗保健中的人工智能'
    }

    # 创建并运行团队
    result = ResearchCrew().crew().kickoff(inputs=inputs)

    # 打印结果
    print("\n\n=== 最终报告 ===\n\n")
    print(result.raw)

    print("\n\n报告已保存到 output/report.md")

if __name__ == "__main__":
    run()
```

这个脚本准备环境，指定我们的研究主题，并启动团队的工作。CrewAI的力量在这段代码的简单性中显而易见 - 管理多个AI代理的所有复杂性都由框架处理。

## 步骤7：设置你的环境变量

在项目根目录创建一个`.env`文件，包含你的API密钥：

```sh  theme={null}
SERPER_API_KEY=your_serper_api_key
# 在这里也添加你提供商的API密钥。
```

有关配置你选择的提供商的详细信息，请参阅[LLM设置指南](/en/concepts/llms#setting-up-your-llm)。你可以从[Serper.dev](https://serper.dev/)获取Serper API密钥。

## 步骤8：安装依赖项

使用CrewAI CLI安装所需的依赖项：

```bash  theme={null}
crewai install
```

这个命令将：

1. 从项目配置中读取依赖项
2. 如果需要则创建虚拟环境
3. 安装所有必需的包

## 步骤9：运行你的团队

现在到了令人激动的时刻 - 是时候运行你的团队并亲眼目睹AI协作了！

```bash  theme={null}
crewai run
```

当你运行这个命令时，你会看到你的团队活灵活现。研究员将收集有关指定主题的信息，然后分析师将基于该研究创建一份全面的报告。当代理们一起完成任务时，你会实时看到他们的思考过程、行动和输出。

## 步骤10：审查输出

团队完成工作后，你会在`output/report.md`文件中找到最终报告。报告将包括：

1. 执行摘要
2. 有关主题的详细信息
3. 分析和见解
4. 建议或未来考虑事项

花点时间欣赏你所完成的成就 - 你创建了一个系统，其中多个AI代理在复杂任务上协作，每个代理贡献其专业技能，产生比任何单个代理单独能实现的更好的结果。

## 探索其他CLI命令

CrewAI提供了几个其他有用的CLI命令来处理团队：

```bash  theme={null}
# 查看所有可用命令
crewai --help

# 运行团队
crewai run

# 测试团队
crewai test

# 重置团队记忆
crewai reset-memories

# 从特定任务重播
crewai replay -t <task_id>
```

## 可能的艺术：超越你的第一个团队

你在本指南中构建的仅仅是个开始。你学到的技能和模式可以应用于创建越来越复杂的AI系统。以下是一些你可以扩展这个基本研究团队的方法：

### 扩展你的团队

你可以向团队添加更多专业代理：

* 一个**事实核查员**，用于验证研究结果
* 一个**数据可视化专家**，用于创建图表和图形
* 一个**领域专家**，在特定领域具有专业知识
* 一个**批评者**，用于识别分析中的弱点

### 添加工具和功能

你可以通过额外的工具增强你的代理：

* 网络浏览工具，用于实时研究
* CSV/数据库工具，用于数据分析
* 代码执行工具，用于数据处理
* 与外部服务的API连接

### 创建更复杂的工作流程

你可以实现更复杂的过程：

* 分层过程，其中管理代理将任务委托给工作代理
* 具有反馈循环的迭代过程，用于精炼
* 并行过程，其中多个代理同时工作
* 基于中间结果调整的动态过程

### 应用于不同领域

相同的模式可以应用于为以下领域创建团队：

* **内容创作**：写手、编辑、事实核查员和设计师一起工作
* **客户服务**：分类代理、专家和质量控制一起工作
* **产品开发**：研究员、设计师和规划师协作
* **数据分析**：数据收集者、分析师和可视化专家

## 下一步

既然你已经构建了你的第一个团队，你可以：

1. 尝试不同的代理配置和个性
2. 尝试更复杂的任务结构和工作流程
3. 实现自定义工具，为你的代理提供新能力
4. 将你的团队应用于不同的主题或问题领域
5. 探索CrewAI流程[Flows](/en/guides/flows/first-flow)，了解使用过程化编程的更高级工作流程

<Check>
  恭喜！你已经成功构建了你的第一个CrewAI团队，可以研究和分析你提供的任何主题。这个基础经验为你提供了创建越来越复杂的AI系统的技能，这些系统可以通过协作智能解决复杂的多阶段问题。
</Check>