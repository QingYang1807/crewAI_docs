# OpenLIT 集成

> 仅需一行代码，通过 OpenTelemetry 快速开始监控您的 Agent。

# OpenLIT 概述

[OpenLIT](https://github.com/openlit/openlit?src=crewai-docs) 是一个开源工具，只需**一行**代码即可简单监控 AI 智能体、大语言模型(LLMs)、向量数据库(VectorDBs)和 GPU 的性能。

它提供原生的 OpenTelemetry 追踪和指标功能，用于跟踪成本、延迟、交互和任务序列等重要参数。
这种设置使您能够跟踪超参数并监控性能问题，帮助您找到方法来持续增强和微调您的智能体。

<Frame caption="OpenLIT 仪表板">
  <img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit1.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=2b7fe9d84e9ec2b33c5eae6fa5668bee" alt="Overview Agent usage including cost and tokens" data-og-width="3024" width="3024" data-og-height="1720" height="1720" data-path="images/openlit1.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit1.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=c971bf515a852fd31020a8b1324f1c1c 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit1.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=84c289d1cb65df00729c0265b937953d 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit1.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=a2f16c0fff510b112190d86e4d660f57 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit1.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=d16913f544b0bab3b1b6df63f9d221eb 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit1.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=f3fb2555418f5a64d567b5ab2c16da87 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit1.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=58ca748851de32f75bfeb36bb2cbdcfa 2500w" />

  <img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit2.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=74bb57f622b6eeb67fd33d93d682df26" alt="Overview of agent otel traces and metrics" data-og-width="3024" width="3024" data-og-height="1728" height="1728" data-path="images/openlit2.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit2.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=5f8faf220f9d7a5660652ec531dd07f9 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit2.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=a02410c4bf920c9aafe972416e52a400 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit2.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=21b69af51e7ed46fb5b38a932a64471b 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit2.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=b32ebaa0e210ff7151bb831d4e2fd5fc 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit2.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=c360d0388bd1af92dedde1967e41017f 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit2.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=9f14c8d65a0ca1177c2aa848c7a57aaa 2500w" />

  <img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit3.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=734cbc0c8fee538986d74b063c20cef6" alt="Overview of agent traces in details" data-og-width="3024" width="3024" data-og-height="1720" height="1720" data-path="images/openlit3.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit3.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=37d3f6eb4f5b66f6466fba0c02137aec 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit3.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=9769fd3fdadf6acd8eadb2f80b4cc352 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit3.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=0f3367e37d8642fe4aa81f444c17b03d 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit3.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=a2c9c17452fde4610823a981d60ce226 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit3.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=bf6526be4832980353137d0e19e38768 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit3.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=a0c09334de9c7d1bb7f90a7ea636379f 2500w" />
</Frame>

### 功能特点

* **分析仪表板**：通过详细仪表板监控您的智能体健康状况和性能，跟踪指标、成本和用户交互。
* **OpenTelemetry 原生可观测性 SDK**：供应商中立的 SDK，可将追踪和指标发送到您现有的可观测性工具，如 Grafana、DataDog 等。
* **自定义和微调模型的成本跟踪**：使用自定义定价文件为特定模型定制成本估算，实现精确预算。
* **异常监控仪表板**：通过监控仪表板跟踪常见异常和错误，快速发现和解决问题。
* **合规与安全**：检测潜在威胁，如亵渎性内容和 PII（个人身份信息）泄露。
* **提示注入检测**：识别潜在的代码注入和密钥泄露。
* **API 密钥和密钥管理**：安全地集中处理您的 LLM API 密钥和密钥，避免不安全做法。
* **提示管理**：使用 PromptHub 管理和版本控制智能体提示，以便在智能体间一致且轻松地访问。
* **模型游乐场**：在部署之前，为您的 CrewAI 智能体测试和比较不同模型。

## 设置说明

<Steps>
  <Step title="部署 OpenLIT">
    <Steps>
      <Step title="Git 克隆 OpenLIT 仓库">
        ```shell  theme={null}
        git clone git@github.com:openlit/openlit.git
        ```
      </Step>

      <Step title="启动 Docker Compose">
        从 [OpenLIT 仓库](https://github.com/openlit/openlit) 的根目录运行以下命令：

        ```shell  theme={null}
        docker compose up -d
        ```
      </Step>
    </Steps>
  </Step>

  <Step title="安装 OpenLIT SDK">
    ```shell  theme={null}
    pip install openlit
    ```
  </Step>

  <Step title="在您的应用程序中初始化 OpenLIT">
    将以下两行代码添加到您的应用程序代码中：

    <Tabs>
      <Tab title="使用函数参数设置">
        ```python  theme={null}
        import openlit
        openlit.init(otlp_endpoint="http://127.0.0.1:4318")
        ```

        监控 CrewAI 智能体的使用示例：

        ```python  theme={null}
        from crewai import Agent, Task, Crew, Process
        import openlit

        openlit.init(disable_metrics=True)
        # 定义您的智能体
        researcher = Agent(
            role="Researcher",
            goal="Conduct thorough research and analysis on AI and AI agents",
            backstory="You're an expert researcher, specialized in technology, software engineering, AI, and startups. You work as a freelancer and are currently researching for a new client.",
            allow_delegation=False,
            llm='command-r'
        )


        # 定义您的任务
        task = Task(
            description="Generate a list of 5 interesting ideas for an article, then write one captivating paragraph for each idea that showcases the potential of a full article on this topic. Return the list of ideas with their paragraphs and your notes.",
            expected_output="5 bullet points, each with a paragraph and accompanying notes.",
        )

        # 定义管理智能体
        manager = Agent(
            role="Project Manager",
            goal="Efficiently manage the crew and ensure high-quality task completion",
            backstory="You're an experienced project manager, skilled in overseeing complex projects and guiding teams to success. Your role is to coordinate the efforts of the crew members, ensuring that each task is completed on time and to the highest standard.",
            allow_delegation=True,
            llm='command-r'
        )

        # 使用自定义管理器实例化您的团队
        crew = Crew(
            agents=[researcher],
            tasks=[task],
            manager_agent=manager,
            process=Process.hierarchical,
        )

        # 启动团队工作
        result = crew.kickoff()

        print(result)
        ```
      </Tab>

      <Tab title="使用环境变量设置">
        将以下两行代码添加到您的应用程序代码中：

        ```python  theme={null}
        import openlit

        openlit.init()
        ```

        运行以下命令来配置 OTEL 导出端点：

        ```shell  theme={null}
        export OTEL_EXPORTER_OTLP_ENDPOINT = "http://127.0.0.1:4318"
        ```

        监控 CrewAI 异步智能体的使用示例：

        ```python  theme={null}
        import asyncio
        from crewai import Crew, Agent, Task
        import openlit

        openlit.init(otlp_endpoint="http://127.0.0.1:4318")

        # 创建一个启用了代码执行的智能体
        coding_agent = Agent(
          role="Python Data Analyst",
          goal="Analyze data and provide insights using Python",
          backstory="You are an experienced data analyst with strong Python skills.",
          allow_code_execution=True,
          llm="command-r"
        )

        # 创建一个需要代码执行的任务
        data_analysis_task = Task(
          description="Analyze the given dataset and calculate the average age of participants. Ages: {ages}",
          agent=coding_agent,
          expected_output="5 bullet points, each with a paragraph and accompanying notes.",
        )

        # 创建一个团队并添加任务
        analysis_crew = Crew(
          agents=[coding_agent],
          tasks=[data_analysis_task]
        )

        # 异步启动团队的异步函数
        async def async_crew_execution():
            result = await analysis_crew.kickoff_async(inputs={"ages": [25, 30, 35, 40, 45]})
            print("Crew Result:", result)

        # 运行异步函数
        asyncio.run(async_crew_execution())
        ```
      </Tab>
    </Tabs>

    更多高级配置和用例，请参考 OpenLIT [Python SDK 仓库](https://github.com/openlit/openlit/tree/main/sdk/python)。
  </Step>

  <Step title="可视化和分析">
    现在，智能体可观测性数据正被收集并发送到 OpenLIT，下一步是可视化和分析这些数据，以获取关于智能体性能、行为的洞察，并识别改进领域。

    只需在浏览器中访问 `127.0.0.1:3000` 上的 OpenLIT 即可开始探索。您可以使用默认凭据登录

    * **邮箱**：`user@openlit.io`
    * **密码**：`openlituser`

    <Frame caption="OpenLIT 仪表板">
      <img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit1.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=2b7fe9d84e9ec2b33c5eae6fa5668bee" alt="Overview Agent usage including cost and tokens" data-og-width="3024" width="3024" data-og-height="1720" height="1720" data-path="images/openlit1.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit1.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=c971bf515a852fd31020a8b1324f1c1c 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit1.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=84c289d1cb65df00729c0265b937953d 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit1.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=a2f16c0fff510b112190d86e4d660f57 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit1.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=d16913f544b0bab3b1b6df63f9d221eb 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit1.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=f3fb2555418f5a64d567b5ab2c16da87 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit1.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=58ca748851de32f75bfeb36bb2cbdcfa 2500w" />

      <img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit2.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=74bb57f622b6eeb67fd33d93d682df26" alt="Overview of agent otel traces and metrics" data-og-width="3024" width="3024" data-og-height="1728" height="1728" data-path="images/openlit2.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit2.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=5f8faf220f9d7a5660652ec531dd07f9 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit2.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=a02410c4bf920c9aafe972416e52a400 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit2.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=21b69af51e7ed46fb5b38a932a64471b 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit2.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=b32ebaa0e210ff7151bb831d4e2fd5fc 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit2.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=c360d0388bd1af92dedde1967e41017f 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/openlit2.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=9f14c8d65a0ca1177c2aa848c7a57aaa 2500w" />
    </Frame>
  </Step>
</Steps>