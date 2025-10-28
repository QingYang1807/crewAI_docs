# MLflow 集成

> 快速开始使用 MLflow 监控您的 Agents。

# MLflow 概述

[MLflow](https://mlflow.org/) 是一个开源平台，旨在帮助机器学习从业者和团队处理机器学习流程中的复杂性。

它提供了一项追踪功能，通过捕获应用程序服务执行的详细信息，来增强生成式 AI 应用中 LLM 的可观测性。追踪功能提供了一种记录每个请求中间步骤的输入、输出和元数据的方法，使您能够轻松定位错误和意外行为的根源。

<img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/mlflow-tracing.gif?s=be88ff36aec776c005102164d804322a" alt="MLflow crewAI 追踪使用概述" data-og-width="1144" width="1144" data-og-height="720" height="720" data-path="images/mlflow-tracing.gif" data-optimize="true" data-opv="3" />

### 功能特性

* **追踪仪表板**：通过包含输入、输出和跨度元数据的详细仪表板，监控您的 crewAI agents 的活动。
* **自动追踪**：与 crewAI 实现完全自动化集成，只需运行 `mlflow.crewai.autolog()` 即可启用。
* **简易的手动追踪**：通过 MLflow 的高级流畅 API（如装饰器、函数包装器和上下文管理器）自定义追踪。
* **OpenTelemetry 兼容性**：MLflow Tracing 支持将追踪导出到 OpenTelemetry Collector，进而可以将追踪导出到各种后端，如 Jaeger、Zipkin 和 AWS X-Ray。
* **打包和部署 Agents**：将您的 crewAI agents 打包并部署到推理服务器，支持多种部署目标。
* **安全托管 LLMs**：通过 MFflow 网关，在统一的端点上托管来自不同提供商的多个 LLM。
* **评估**：使用便捷的 API `mlflow.evaluate()`，通过多种指标评估您的 crewAI agents。

## 安装说明

<Steps>
  <Step title="安装 MLflow 包">
    ```shell  theme={null}
    # crewAI 集成在 mlflow>=2.19.0 版本中可用
    pip install mlflow
    ```
  </Step>

  <Step title="启动 MFflow 追踪服务器">
    ```shell  theme={null}
    # 此过程是可选的，但建议使用 MLflow 追踪服务器以获得更好的可视化效果和更广泛的功能。
    mlflow server
    ```
  </Step>

  <Step title="在您的应用程序中初始化 MLflow">
    在您的应用程序代码中添加以下两行：

    ```python  theme={null}
    import mlflow

    mlflow.crewai.autolog()

    # 可选：如果您有追踪服务器，请设置追踪 URI 和实验名称
    mlflow.set_tracking_uri("http://localhost:5000")
    mlflow.set_experiment("CrewAI")
    ```

    追踪 CrewAI Agents 的使用示例：

    ```python  theme={null}
    from crewai import Agent, Crew, Task
    from crewai.knowledge.source.string_knowledge_source import StringKnowledgeSource
    from crewai_tools import SerperDevTool, WebsiteSearchTool

    from textwrap import dedent

    content = "用户名叫 John，他 30 岁，住在旧金山。"
    string_source = StringKnowledgeSource(
        content=content, metadata={"preference": "personal"}
    )

    search_tool = WebsiteSearchTool()


    class TripAgents:
        def city_selection_agent(self):
            return Agent(
                role="城市选择专家",
                goal="根据天气、季节和价格选择最佳城市",
                backstory="一位分析旅行数据以挑选理想目的地的专家",
                tools=[
                    search_tool,
                ],
                verbose=True,
            )

        def local_expert(self):
            return Agent(
                role="本地专家",
                goal="提供关于所选城市的最佳见解",
                backstory="""一位知识渊博的当地向导，对该城市的
            景点和风俗习惯有广泛的信息""",
                tools=[search_tool],
                verbose=True,
            )


    class TripTasks:
        def identify_task(self, agent, origin, cities, interests, range):
            return Task(
                description=dedent(
                    f"""
                    根据天气模式、季节性事件和旅行费用等具体标准，
                    分析并选择旅行的最佳城市。此任务涉及比较多个城市，
                    考虑因素如当前天气状况、即将到来的文化或季节性事件以及总体旅行费用。
                    您的最终答案必须是关于所选城市的详细报告，
                    包括您发现的所有信息，如实际航班费用、天气预报和景点。

                    出发地：{origin}
                    城市选项：{cities}
                    旅行日期：{range}
                    旅行者兴趣：{interests}
                """
                ),
                agent=agent,
                expected_output="关于所选城市的详细报告，包括航班费用、天气预报和景点",
            )

        def gather_task(self, agent, origin, interests, range):
            return Task(
                description=dedent(
                    f"""
                    作为该城市的本地专家，您必须为前往该地并希望拥有
                    最棒旅行体验的人编制一份深入指南！收集关于关键景点、
                    当地习俗、特殊活动和日常活动建议的信息。
                    寻找最佳去处，那些只有当地人才知道的地方。
                    这份指南应全面概述该城市能提供的一切，包括隐藏的瑰宝、
                    文化热点、必游地标、天气预报和高水平费用。
                    最终答案必须是一份全面的城市指南，
                    丰富的文化见解和实用技巧，旨在增强旅行体验。

                    旅行日期：{range}
                    出发地：{origin}
                    旅行者兴趣：{interests}
                """
                ),
                agent=agent,
                expected_output="全面的城市指南，包括隐藏的瑰宝、文化热点和实用的旅行技巧",
            )


    class TripCrew:
        def __init__(self, origin, cities, date_range, interests):
            self.cities = cities
            self.origin = origin
            self.interests = interests
            self.date_range = date_range

        def run(self):
            agents = TripAgents()
            tasks = TripTasks()

            city_selector_agent = agents.city_selection_agent()
            local_expert_agent = agents.local_expert()

            identify_task = tasks.identify_task(
                city_selector_agent,
                self.origin,
                self.cities,
                self.interests,
                self.date_range,
            )
            gather_task = tasks.gather_task(
                local_expert_agent, self.origin, self.interests, self.date_range
            )

            crew = Crew(
                agents=[city_selector_agent, local_expert_agent],
                tasks=[identify_task, gather_task],
                verbose=True,
                memory=True,
                knowledge={
                    "sources": [string_source],
                    "metadata": {"preference": "personal"},
                },
            )

            result = crew.kickoff()
            return result


    trip_crew = TripCrew("加利福尼亚", "东京", "12月12日 - 12月20日", "运动")
    result = trip_crew.run()

    print(result)
    ```

    更多配置和用例请参考 [MLflow Tracing 文档](https://mlflow.org/docs/latest/llms/tracing/index.html)。
  </Step>

  <Step title="可视化 Agent 活动">
    现在，您的 crewAI agents 的追踪信息已被 MLflow 捕获。
    让我们访问 MLflow 追踪服务器来查看追踪信息，并深入了解您的 Agents。

    在浏览器中打开 `127.0.0.1:5000` 以访问 MLflow 追踪服务器。

    <Frame caption="MLflow Tracing 仪表板">
      <img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/mlflow1.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=0685aeb79319ad21ff842053ce5303c9" alt="MLflow 与 crewai 的追踪示例" data-og-width="3024" width="3024" data-og-height="1712" height="1712" data-path="images/mlflow1.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/mlflow1.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=f45f73922d8ea58f0e72c16c0e22d977 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/mlflow1.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=5025f431d9c88bb7a094ab032aeaa753 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/mlflow1.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=385879dd49ee957a4c25c5f94a9766e1 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/mlflow1.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=c779a19494038367ecb7f1caefe1014c 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/mlflow1.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=ff45740a0aaab934952c2928edbdcbc6 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/mlflow1.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=af14478b932df5a9f9261514f38d5e14 2500w" />
    </Frame>
  </Step>
</Steps>