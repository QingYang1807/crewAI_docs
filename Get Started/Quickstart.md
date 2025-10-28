# 快速入门

> 用不到 5 分钟的时间，使用 CrewAI 构建你的第一个 AI 代理。

## 构建你的第一个 CrewAI 代理

让我们创建一个简单的团队，它会帮助我们针对给定主题或科目 `研究` 并 `报告` `最新 AI 发展动态`。

在继续之前，请确保你已经完成了 CrewAI 的安装。
如果尚未安装，可以按照 [安装指南](/en/installation) 进行操作。

请遵循以下步骤开始使用 CrewAI！🚣‍♂️

<Steps>
  <Step title="创建你的团队">
    在终端中运行以下命令，创建一个新的团队项目。
    这将创建一个名为 `latest-ai-development` 的新目录，其中包含你的团队所需的基本结构。

    <CodeGroup>
      ```shell Terminal theme={null}
      crewai create crew latest-ai-development
      ```
    </CodeGroup>
  </Step>

  <Step title="导航到你的新团队项目">
    <CodeGroup>
      ```shell Terminal theme={null}
      cd latest_ai_development
      ```
    </CodeGroup>
  </Step>

  <Step title="修改你的 `agents.yaml` 文件">
    <Tip>
      你也可以根据需要修改代理以适应你的用例，或者原样复制粘贴到你的项目中。
      在 `agents.yaml` 和 `tasks.yaml` 文件中任何像 `{topic}` 这样的插值变量，都将被 `main.py` 文件中该变量的值所替换。
    </Tip>

    ```yaml agents.yaml theme={null}
    # src/latest_ai_development/config/agents.yaml
    researcher:
      role: >
        {topic} 高级数据研究员
      goal: >
        揭示 {topic} 领域的前沿发展
      backstory: >
        你是一位经验丰富的研究员，擅长发现 {topic} 领域的
        最新进展。你以能够找到最相关的信息并
        以清晰简洁的方式呈现而闻名。

    reporting_analyst:
      role: >
        {topic} 报告分析师
      goal: >
        基于 {topic} 的数据分析和研究结果创建详细的报告
      backstory: >
        你是一位一丝不苟的分析师，对细节有敏锐的洞察力。你以
        将复杂数据转化为清晰简洁的报告而闻名，使
        其他人能够轻松理解并根据你提供的信息采取行动。
    ```
  </Step>

  <Step title="修改你的 `tasks.yaml` 文件">
    ````yaml tasks.yaml theme={null}
    # src/latest_ai_development/config/tasks.yaml
    research_task:
      description: >
        对 {topic} 进行全面的研究
        确保你找到任何有趣且相关的信息，并
        注意当前年份是 2025 年。
      expected_output: >
        一份包含 10 个要点的列表，列出关于 {topic} 的最相关信息
      agent: researcher

    reporting_task:
      description: >
        审查你获得的上下文，并将每个主题扩展为报告的一个完整部分。
        确保报告详细，包含所有相关信息。
      expected_output: >
        一份完整的报告，包含主要主题，每个主题都有一个完整的信息部分。
        格式化为 markdown，不包含 '```'
      agent: reporting_analyst
      output_file: report.md
    ````
  </Step>

  <Step title="修改你的 `crew.py` 文件">
    ```python crew.py theme={null}
    # src/latest_ai_development/crew.py
    from crewai import Agent, Crew, Process, Task
    from crewai.project import CrewBase, agent, crew, task
    from crewai_tools import SerperDevTool
    from crewai.agents.agent_builder.base_agent import BaseAgent
    from typing import List

    @CrewBase
    class LatestAiDevelopmentCrew():
      """LatestAiDevelopment crew"""

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
      def reporting_analyst(self) -> Agent:
        return Agent(
          config=self.agents_config['reporting_analyst'], # type: ignore[index]
          verbose=True
        )

      @task
      def research_task(self) -> Task:
        return Task(
          config=self.tasks_config['research_task'], # type: ignore[index]
        )

      @task
      def reporting_task(self) -> Task:
        return Task(
          config=self.tasks_config['reporting_task'], # type: ignore[index]
          output_file='output/report.md' # This is the file that will be contain the final report.
        )

      @crew
      def crew(self) -> Crew:
        """Creates the LatestAiDevelopment crew"""
        return Crew(
          agents=self.agents, # Automatically created by the @agent decorator
          tasks=self.tasks, # Automatically created by the @task decorator
          process=Process.sequential,
          verbose=True,
        )
    ```
  </Step>

  <Step title="[可选] 添加团队启动前后的函数">
    ```python crew.py theme={null}
    # src/latest_ai_development/crew.py
    from crewai import Agent, Crew, Process, Task
    from crewai.project import CrewBase, agent, crew, task, before_kickoff, after_kickoff
    from crewai_tools import SerperDevTool

    @CrewBase
    class LatestAiDevelopmentCrew():
      """LatestAiDevelopment crew"""

      @before_kickoff
      def before_kickoff_function(self, inputs):
        print(f"Before kickoff function with inputs: {inputs}")
        return inputs # You can return the inputs or modify them as needed

      @after_kickoff
      def after_kickoff_function(self, result):
        print(f"After kickoff function with result: {result}")
        return result # You can return the result or modify them as needed

      # ... remaining code
    ```
  </Step>

  <Step title="随意向你的团队传递自定义输入">
    例如，你可以向你的团队传递 `topic` 输入，以自定义研究和报告。

    ```python main.py theme={null}
    #!/usr/bin/env python
    # src/latest_ai_development/main.py
    import sys
    from latest_ai_development.crew import LatestAiDevelopmentCrew

    def run():
      """
      运行团队。
      """
      inputs = {
        'topic': 'AI 代理'
      }
      LatestAiDevelopmentCrew().crew().kickoff(inputs=inputs)
    ```
  </Step>

  <Step title="设置你的环境变量">
    在运行你的团队之前，请确保你已在 `.env` 文件中将以下密钥设置为环境变量：

    * 一个 [Serper.dev](https://serper.dev/) API 密钥：`SERPER_API_KEY=YOUR_KEY_HERE`
    * 你所选模型的配置，例如 API 密钥。请参阅
      [LLM 设置指南](/en/concepts/llms#setting-up-your-llm) 以了解如何配置来自任何提供商的模型。
  </Step>

  <Step title="锁定并安装依赖项">
    * 使用 CLI 命令锁定依赖项并安装它们：
      <CodeGroup>
        ```shell Terminal theme={null}
        crewai install
        ```
      </CodeGroup>
    * 如果你想安装其他软件包，可以运行：
      <CodeGroup>
        ```shell Terminal theme={null}
        uv add <package-name>
        ```
      </CodeGroup>
  </Step>

  <Step title="运行你的团队">
    * 要运行你的团队，请在项目的根目录中执行以下命令：
      <CodeGroup>
        ```bash Terminal theme={null}
        crewai run
        ```
      </CodeGroup>
  </Step>

  <Step title="企业级替代方案：在 Crew Studio 中创建">
    对于 CrewAI AMP 用户，你可以无需编写代码创建相同的团队：

    1. 登录你的 CrewAI AMP 账户（在 [app.crewai.com](https://app.crewai.com) 创建免费账户）
    2. 打开 Crew Studio
    3. 输入你想要构建的自动化内容
    4. 可视化创建你的任务并按顺序连接它们
    5. 配置你的输入并点击“下载代码”或“部署”

        <img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=c4f5428b111816273b3b53d9cef14fad" alt="Crew Studio Quickstart" data-og-width="2654" width="2654" data-og-height="1710" height="1710" data-path="images/enterprise/crew-studio-interface.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=35ea9140f0b9e57da5f45adbc7e2f166 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=ae6f0c18ef3679b5466177710fbc4a94 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=6c3e2fe013ab4826da90c937a9855635 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=7f1474dd7f983532dc910363b96f783a 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=f1a6d7e744e6862af5e72dce4deb0fd1 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/crew-studio-interface.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=74aeb1ccd8e2c8f84d4247b8d0259737 2500w" />

    <Card title="试试 CrewAI AMP" icon="rocket" href="https://app.crewai.com">
      在 CrewAI AMP 开始你的免费账户
    </Card>
  </Step>

  <Step title="查看你的最终报告">
    你应该在控制台中看到输出，并且在项目的根目录中会创建 `report.md` 文件，其中包含最终报告。

    以下是报告应如下的示例：

    <CodeGroup>
      ```markdown output/report.md theme={null}
      # 2025年AI代理的兴起与影响综合报告

      ## 1. AI代理简介
      2025年，人工智能（AI）代理在各行各业的创新中处于前沿地位。作为能够执行通常需要人类认知任务的智能系统，AI代理正在为人力资源（HR）和金融等领域的运营效率、决策制定和整体生产力带来重大进步。本报告旨在详细介绍AI代理的兴起、其框架、应用以及对劳动力的潜在影响。

      ## 2. AI代理的优势
      AI代理带来了众多优势，正在改变传统的工作环境。主要优势包括：

      - **任务自动化**：AI代理可以在无人干预的情况下执行重复性任务，如数据录入、调度和薪资处理，从而大大减少这些活动所花费的时间和资源。
      - **提高效率**：通过快速处理大型数据集并执行人类需要更长时间才能完成的分析，AI代理提高了运营效率。这使得团队可以专注于需要更高层次思维的战略任务。
      - **增强决策能力**：AI代理可以分析数据中的趋势和模式，提供见解，甚至提出行动建议，帮助利益相关者基于事实数据而非仅仅依靠直觉做出明智决策。

      ## 3. 流行的AI代理框架
      出现了几个框架来促进AI代理的开发，每个框架都有其独特的特性和能力。一些最受欢迎的框架包括：

      - **Autogen**：一个旨在通过自动化代码生成来简化AI代理开发的框架。
      - **Semantic Kernel**：专注于自然语言处理和理解，使代理能够更好地理解用户意图。
      - **Promptflow**：为开发者提供工具来创建能够无缝处理复杂交互的对话代理。
      - **Langchain**：专门利用各种API，确保代理能够有效访问和利用外部数据。
      - **CrewAI**：针对协作环境，CrewAI通过促进由AI驱动的洞察来加强团队合作。
      - **MemGPT**：将内存优化的架构与生成能力相结合，允许与用户进行更个性化的交互。

      这些框架使开发者能够构建多功能和智能的代理，能够与用户互动、执行高级分析并完成与组织目标一致的各种任务。

      ## 4. 人力资源中的AI代理
      AI代理通过自动化和优化关键功能正在彻底改变HR实践：

      - **招聘**：AI代理可以筛选简历、安排面试，甚至进行初步评估，从而加快招聘流程，同时最大限度地减少偏见。
      - **继任计划**：AI系统分析员工绩效数据和潜力，帮助组织识别未来的领导者并规划适当的培训。
      - **员工敬业度**：由AI驱动的聊天机器人可以促进员工与管理层之间的反馈循环，促进开放文化并及时解决问题。

      随着AI的不断发展，利用这些代理的HR部门可以在效率和员工满意度方面实现实质性的提升。

      ## 5. 金融领域的AI代理
      金融行业正在广泛集成AI代理，以增强金融实践：

      - **费用跟踪**：自动化系统管理和监控费用，标记异常并根据支出模式提供建议。
      - **风险评估**：AI模型通过分析交易数据和行为模式来评估信用风险和发现潜在欺诈。
      - **投资决策**：AI代理基于历史数据和当前市场条件提供股票预测和分析，为投资者提供有价值的见解。

      AI代理在金融领域的应用正在催生一个响应更快、风险意识更强的金融环境。

      ## 6. 市场趋势与投资
      AI代理的增长吸引了大量投资，尤其是在聊天机器人和生成式AI技术日益普及的背景下。公司和企业家都渴望探索这些系统的潜力，认识到它们在简化运营和改善客户参与度方面的能力。

      与此同时，像微软这样的公司正在努力将AI代理集成到其产品中，例如对其Copilot 365应用程序的增强。这一战略举措强调了AI素养在现代工作场所的重要性，并表明AI代理正成为必不可少的商业工具。

      ## 7. 未来预测与影响
      专家预测，AI代理将改变工作生活的基本方面。展望未来，几个预期的变化包括：

      - 在所有业务功能中增强AI代理的集成，创建能够利用各部门数据孤岛数据进行综合决策的互联系统。
      - AI技术的持续进步，将带来更智能、适应性更强的代理，能够从用户交互中学习和进化。
      - 加强监管审查以确保合乎道德的使用，特别是在数据隐私和员工监控方面，因为AI代理变得越来越普遍。

      为了保持竞争力并充分利用AI代理的潜力，组织必须对AI技术的最新发展保持警惕，并在其战略规划中考虑持续学习和适应。

      ## 8. 结论
      毫无疑问，AI代理的出现正在重塑2025年的工作环境。凭借其自动化任务、提高效率和改善决策的能力，AI代理在推动运营成功方面至关重要。组织必须拥抱并适应AI的发展，才能在日益数字化的商业环境中蓬勃发展。
      ```
    </CodeGroup>
  </Step>
</Steps>

<Check>
  恭喜！

  你已成功设置好你的团队项目，并准备好开始构建你自己的代理工作流！
</Check>

### 关于命名一致性的说明

你在 YAML 文件（`agents.yaml` 和 `tasks.yaml`）中使用的名称应与 Python 代码中的方法名称相匹配。
例如，你可以从 `tasks.yaml` 文件中引用特定任务的代理。
这种命名一致性允许 CrewAI 自动将你的配置与代码关联起来；否则，你的任务将无法正确识别引用。

#### 示例引用

<Tip>
  注意，我们在 `agents.yaml` 文件中为代理使用的名称（`email_summarizer`）与 `crew.py` 文件中的方法名称（`email_summarizer`）相同。
</Tip>

```yaml agents.yaml theme={null}
email_summarizer:
    role: >
      邮件摘要器
    goal: >
      将邮件总结为简洁明了的摘要
    backstory: >
      你将创建一个包含5个要点的报告摘要
    llm: provider/model-id  # 在这里添加你选择的模型
```

<Tip>
  注意，我们在 `tasks.yaml` 文件中为任务使用的名称（`email_summarizer_task`）与 `crew.py` 文件中的方法名称（`email_summarizer_task`）相同。
</Tip>

```yaml tasks.yaml theme={null}
email_summarizer_task:
    description: >
      将邮件总结为5个要点的摘要
    expected_output: >
      一份包含5个要点的邮件摘要
    agent: email_summarizer
    context:
      - reporting_task
      - research_task
```

## 部署你的团队

将你的团队部署到生产环境的最简单方法是通过 [CrewAI AMP](http://app.crewai.com)。

观看此视频教程，了解如何使用 CLI 将你的团队部署到 [CrewAI AMP](http://app.crewai.com) 的分步演示。

<iframe className="w-full aspect-video rounded-xl" src="https://www.youtube.com/embed/3EqSV-CYDZA" title="CrewAI Deployment Guide" frameBorder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowFullScreen />

<CardGroup cols={2}>
  <Card title="部署到企业版" icon="rocket" href="http://app.crewai.com">
    开始使用 CrewAI AMP，只需点击几下即可将你的团队部署到生产环境中。
  </Card>

  <Card title="加入社区" icon="comments" href="https://community.crewai.com">
    加入我们的开源社区，讨论想法、分享你的项目，并与其他 CrewAI 开发者建立联系。
  </Card>
</CardGroup>