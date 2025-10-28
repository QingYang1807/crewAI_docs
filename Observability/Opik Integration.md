# Opik 集成

> 了解如何使用 Comet Opik 通过全面的追踪、自动评估和生产就绪的仪表板来调试、评估和监控您的 CrewAI 应用程序。

# Opik 概述

使用 [Comet Opik](https://www.comet.com/docs/opik/)，通过全面的追踪、自动评估和生产就绪的仪表板来调试、评估和监控您的 LLM 应用程序、RAG 系统和代理工作流程。

<Frame caption="Opik 代理仪表板">
  <img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/opik-crewai-dashboard.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=6b313c7d767211f2287d7dd074f9dfeb" alt="使用 CrewAI 的 Opik 代理监控示例" data-og-width="1538" width="1538" data-og-height="877" height="877" data-path="images/opik-crewai-dashboard.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/opik-crewai-dashboard.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=b0fd3eca42762838a806dc0d38d0313f 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/opik-crewai-dashboard.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=ba1166f8b42afee86b3cd88532002fde 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/opik-crewai-dashboard.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=d7905602ba2ac1dbca66a008be49ca26 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/opik-crewai-dashboard.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=51c2c8ce83af0d81fdf851dd378fe6f6 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/opik-crewai-dashboard.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=5184c8370b92c51a03d970a01c7daba5 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/opik-crewai-dashboard.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=80d3fb638cd8ace74953775242cb68cd 2500w" />
</Frame>

Opik 为您的 CrewAI 应用程序开发的每个阶段提供全面支持：

* **记录追踪和跨度**：自动跟踪 LLM 调用和应用程序逻辑，以调试和分析开发和生产系统。手动或以编程方式注释、查看和比较跨项目的响应。
* **评估您的 LLM 应用程序性能**：根据自定义测试集进行评估，运行内置评估指标或在 SDK 或 UI 中定义您自己的指标。
* **在您的 CI/CD 管道中进行测试**：使用 Opik 基于 PyTest 构建的 LLM 单元测试建立可靠的性能基线。运行在线评估以在生产环境中进行持续监控。
* **监控和分析生产数据**：了解您的模型在生产中对未见数据的性能，并为新的开发迭代生成数据集。

## 设置

Comet 提供了 Opik 平台的托管版本，或者您可以在本地运行该平台。

要使用托管版本，只需[创建一个免费的 Comet 账户](https://www.comet.com/signup?utm_medium=github\&utm_source=crewai_docs)并获取您的 API 密钥。

要在本地运行 Opik 平台，请参阅我们的[安装指南](https://www.comet.com/docs/opik/self-host/overview/)获取更多信息。

在本指南中，我们将使用 CrewAI 的快速入门示例。

<Steps>
  <Step title="安装必需的包">
    ```shell  theme={null}
    pip install crewai crewai-tools opik --upgrade
    ```
  </Step>

  <Step title="配置 Opik">
    ```python  theme={null}
    import opik
    opik.configure(use_local=False)
    ```
  </Step>

  <Step title="准备环境">
    首先，我们将我们的 LLM 提供商的 API 密钥设置为环境变量：

    ```python  theme={null}
    import os
    import getpass

    if "OPENAI_API_KEY" not in os.environ:
    os.environ["OPENAI_API_KEY"] = getpass.getpass("输入您的 OpenAI API 密钥: ")
    ```
  </Step>

  <Step title="使用 CrewAI">
    第一步是创建我们的项目。我们将使用 CrewAI 文档中的一个示例：

    ```python  theme={null}
    from crewai import Agent, Crew, Task, Process


    class YourCrewName:
        def agent_one(self) -> Agent:
            return Agent(
                role="数据分析师",
                goal="分析市场数据趋势",
                backstory="一位具有经济学背景的经验丰富的数据分析师",
                verbose=True,
            )

        def agent_two(self) -> Agent:
            return Agent(
                role="市场研究员",
                goal="收集市场动态信息",
                backstory="一位勤奋的研究员，对细节有敏锐的洞察力",
                verbose=True,
            )

        def task_one(self) -> Task:
            return Task(
                name="收集数据任务",
                description="收集最近的市场数据并确定趋势。",
                expected_output="一份总结市场关键趋势的报告。",
                agent=self.agent_one(),
            )

        def task_two(self) -> Task:
            return Task(
                name="市场研究任务",
                description="研究影响市场动态的因素。",
                expected_output="对影响市场因素的分析。",
                agent=self.agent_two(),
            )

        def crew(self) -> Crew:
            return Crew(
                agents=[self.agent_one(), self.agent_two()],
                tasks=[self.task_one(), self.task_two()],
                process=Process.sequential,
                verbose=True,
            )

    ```

    现在我们可以导入 Opik 的跟踪器并运行我们的 crew：

    ```python  theme={null}
    from opik.integrations.crewai import track_crewai

    track_crewai(project_name="crewai-integration-demo")

    my_crew = YourCrewName().crew()
    result = my_crew.kickoff()

    print(result)
    ```

    运行您的 CrewAI 应用程序后，访问 Opik 应用程序查看：

    * LLM 追踪、跨度及其元数据
    * 代理交互和任务执行流程
    * 延迟和令牌使用等性能指标
    * 评估指标（内置或自定义）
  </Step>
</Steps>

## 资源

* [🦉 Opik 文档](https://www.comet.com/docs/opik/)
* [👉 Opik + CrewAI Colab](https://colab.research.google.com/github/comet-ml/opik/blob/main/apps/opik-documentation/documentation/docs/cookbook/crewai.ipynb)
* [🐦 X](https://x.com/cometml)
* [💬 Slack](https://slack.comet.com/)