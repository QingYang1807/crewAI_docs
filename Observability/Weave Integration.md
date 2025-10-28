# Weave 集成

> 了解如何使用 Weights & Biases (W&B) Weave 来跟踪、实验、评估和改进您的 CrewAI 应用程序。

## Weave 概述

[Weights & Biases (W\&B) Weave](https://weave-docs.wandb.ai/) 是一个用于跟踪、实验、评估、部署和改进基于 LLM 的应用程序的框架。

<img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/weave-tracing.gif?s=4a933830e3e3cf146c4c87cb44d46475" alt="W&B Weave CrewAI 追踪使用概述" data-og-width="852" width="852" data-og-height="480" height="480" data-path="images/weave-tracing.gif" data-optimize="true" data-opv="3" />

Weave 为您的 CrewAI 应用程序开发的每个阶段提供全面支持：

* **追踪与监控**：自动跟踪 LLM 调用和应用程序逻辑，以调试和分析生产系统
* **系统化迭代**：优化和迭代提示、数据集和模型
* **评估**：使用自定义或预构建的评分器系统性地评估和增强代理性能
* **护栏**：通过前后保护措施保护您的代理，实现内容审核和提示安全

Weave 自动为您的 CrewAI 应用程序捕获追踪记录，使您能够监控和分析代理的性能、交互和执行流程。这有助于您构建更好的评估数据集并优化代理工作流程。

## 设置说明

<Steps>
  <Step title="安装所需的包">
    ```shell  theme={null}
    pip install crewai weave
    ```
  </Step>

  <Step title="设置 W&B 账户">
    如果您还没有 [Weights & Biases 账户](https://wandb.ai)，请注册一个。您需要这个账户来查看您的追踪记录和指标。
  </Step>

  <Step title="在您的应用程序中初始化 Weave">
    将以下代码添加到您的应用程序中：

    ```python  theme={null}
    import weave

    # 使用您的项目名称初始化 Weave
    weave.init(project_name="crewai_demo")
    ```

    初始化后，Weave 将提供一个 URL，您可以在那里查看您的追踪记录和指标。
  </Step>

  <Step title="创建您的 Crew/Flow">
    ```python  theme={null}
    from crewai import Agent, Task, Crew, LLM, Process

    # 创建一个温度为 0 的 LLM 以确保确定性输出
    llm = LLM(model="gpt-4o", temperature=0)

    # 创建代理
    researcher = Agent(
        role='Research Analyst',
        goal='Find and analyze the best investment opportunities',
        backstory='Expert in financial analysis and market research',
        llm=llm,
        verbose=True,
        allow_delegation=False,
    )

    writer = Agent(
        role='Report Writer',
        goal='Write clear and concise investment reports',
        backstory='Experienced in creating detailed financial reports',
        llm=llm,
        verbose=True,
        allow_delegation=False,
    )

    # 创建任务
    research_task = Task(
        description='Deep research on the {topic}',
        expected_output='Comprehensive market data including key players, market size, and growth trends.',
        agent=researcher
    )

    writing_task = Task(
        description='Write a detailed report based on the research',
        expected_output='The report should be easy to read and understand. Use bullet points where applicable.',
        agent=writer
    )

    # 创建一个团队
    crew = Crew(
        agents=[researcher, writer],
        tasks=[research_task, writing_task],
        verbose=True,
        process=Process.sequential,
    )

    # 运行团队
    result = crew.kickoff(inputs={"topic": "AI in material science"})
    print(result)
    ```
  </Step>

  <Step title="在 Weave 中查看追踪记录">
    运行您的 CrewAI 应用程序后，访问初始化期间提供的 Weave URL 以查看：

    * LLM 调用及其元数据
    * 代理交互和任务执行流程
    * 延迟和令牌使用等性能指标
    * 执行过程中发生的任何错误或问题

    <Frame caption="Weave 追踪仪表板">
      <img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/weave-tracing.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=f59e556fcc0ac8fcca8eaeef4c0551ae" alt="CrewAI 的 Weave 追踪示例" data-og-width="3456" width="3456" data-og-height="1986" height="1986" data-path="images/weave-tracing.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/weave-tracing.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=42efb320cedff3209765027d4f47e187 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/weave-tracing.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=85318181f2afd6237c71cecedfda8104 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/weave-tracing.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=6124c3113060320d39847c47faa02ac4 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/weave-tracing.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=e04300ff38ddf3624acc078bacf6712e 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/weave-tracing.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=c0ba473c3fa41c2939df4e28bc1098b5 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/weave-tracing.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=6d860d735bc5a42fb68085fc4ef01b2c 2500w" />
    </Frame>
  </Step>
</Steps>

## 功能特性

* Weave 自动捕获所有 CrewAI 操作：代理交互和任务执行；带有元数据和令牌使用情况的 LLM 调用；工具使用和结果。
* 该集成支持所有 CrewAI 执行方法：`kickoff()`、`kickoff_for_each()`、`kickoff_async()` 和 `kickoff_for_each_async()`。
* 自动追踪所有 [crewAI-tools](https://github.com/crewAIInc/crewAI-tools)。
* 通过装饰器补丁（`@start`、`@listen`、`@router`、`@or_`、`@and_`）支持 Flow 功能。
* 使用 `@weave.op()` 跟踪传递给 CrewAI `Task` 的自定义护栏。

有关所支持功能的详细信息，请访问 [Weave CrewAI 文档](https://weave-docs.wandb.ai/guides/integrations/crewai/#getting-started-with-flow)。

## 资源

* [📘 Weave 文档](https://weave-docs.wandb.ai)
* [📊 Weave x CrewAI 仪表板示例](https://wandb.ai/ayut/crewai_demo/weave/traces?cols=%7B%22wb_run_id%22%3Afalse%2C%22attributes.weave.client_version%22%3Afalse%2C%22attributes.weave.os_name%22%3Afalse%2C%22attributes.weave.os_release%22%3Afalse%2C%22attributes.weave.os_version%22%3Afalse%2C%22attributes.weave.source%22%3Afalse%2C%22attributes.weave.sys_version%22%3Afalse%7D\&peekPath=%2Fayut%2Fcrewai_demo%2Fcalls%2F0195c838-38cb-71a2-8a15-651ecddf9d89)
* [🐦 X](https://x.com/weave_wb)