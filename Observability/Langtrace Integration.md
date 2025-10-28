# Langtrace 集成

> 如何使用 Langtrace（一个外部可观测性工具）来监控 CrewAI 智能体的成本、延迟和性能。

# Langtrace 概述

Langtrace 是一个开源的外部工具，帮助您为大型语言模型（LLM）、LLM 框架和向量数据库建立可观测性和评估系统。
虽然 Langtrace 不是直接内置在 CrewAI 中的，但它可以与 CrewAI 一起使用，让您深入了解 CrewAI 智能体的成本、延迟和性能。
这种集成允许您记录超参数、监控性能回归，并为智能体的持续改进建立流程。

<img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace1.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=85b67e42028ca9383087737279f8931f" alt="一系列选定的智能体会话运行概览" data-og-width="1717" width="1717" data-og-height="1299" height="1299" data-path="images/langtrace1.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace1.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=ba769f765ef36edf033e2caf8c7df4c7 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace1.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=9e24743472c91a4b42dc235c8b16691a 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace1.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=f70296efa8dd1619670008b9af8d7e78 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace1.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=c8281aea8f979aa38ff1b6519e3fb840 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace1.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=c55eafbe3e6fee44aaa208f8ce988847 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace1.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=677e866fb2e110afc4e7f6800abafdc3 2500w" />
<img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace2.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=24f08e5c56b6200e386d305a7bee347c" alt="智能体追踪概览" data-og-width="1725" width="1725" data-og-height="1094" height="1094" data-path="images/langtrace2.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace2.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=9182a51d34df98e5a5b7b994b6d11d4d 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace2.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=fa10f203c90f5394c5a3fe5b96b52685 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace2.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=1a1d34a1ac4c2d2d5a4748e4a4a46c97 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace2.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=81bb4d002d59c63f80c2cff7f2afbadd 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace2.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=de0fe6458f4595f73615ce7acb6b5d37 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace2.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=1cf56b30d10a0ef841aa762fa27c74a2 2500w" />
<img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace3.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=f1a8624e0c05d59deded640e4751a986" alt="详细的大语言模型追踪概览" data-og-width="1710" width="1710" data-og-height="1217" height="1217" data-path="images/langtrace3.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace3.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=3483f588b90a032d4919847e6e63772a 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace3.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=c638c41d0e0e156c2c2e0ed1e80bfde1 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace3.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=6d2b588d0a6c176a8fd80e4d3844d955 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace3.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=611c9167a54facb43c95a2239bba6a39 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace3.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=62a6bfc0052934c423ce245ddef1a28e 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/langtrace3.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=726ee5e7feed10e6f507233807e70ae9 2500w" />

## 设置说明

<Steps>
  <Step title="注册 Langtrace">
    访问 [https://langtrace.ai/signup](https://langtrace.ai/signup) 进行注册。
  </Step>

  <Step title="创建项目">
    将项目类型设置为 `CrewAI` 并生成 API 密钥。
  </Step>

  <Step title="在 CrewAI 项目中安装 Langtrace">
    使用以下命令：

    ```bash  theme={null}
    pip install langtrace-python-sdk
    ```
  </Step>

  <Step title="导入 Langtrace">
    在脚本开头导入并初始化 Langtrace，在任何 CrewAI 导入之前：

    ```python  theme={null}
    from langtrace_python_sdk import langtrace
    langtrace.init(api_key='<LANGTRACE_API_KEY>')

    # 现在导入 CrewAI 模块
    from crewai import Agent, Task, Crew
    ```
  </Step>
</Steps>

### 功能及其在 CrewAI 中的应用

1. **LLM 令牌和成本跟踪**

   * 监控每个 CrewAI 智能体交互的令牌使用量和相关成本。

2. **执行步骤的追踪图**

   * 可视化您的 CrewAI 任务的执行流程，包括延迟和日志。
   * 有助于识别智能体工作流程中的瓶颈。

3. **带手动标注的数据集整理**

   * 根据您的 CrewAI 任务输出创建数据集，用于未来的训练或评估。

4. **提示版本控制和管理**

   * 跟踪 CrewAI 智能体中使用的不同版本的提示。
   * 有助于 A/B 测试和优化智能体性能。

5. **带模型比较的提示测试场**

   * 在部署前测试和比较 CrewAI 智能体的不同提示和模型。

6. **测试和评估**

   * 为您的 CrewAI 智能体和任务设置自动化测试。