# 流程 (Flows)

> 了解如何使用 CrewAI Flows 创建和管理 AI 工作流程。

## 概述

CrewAI Flows 是一个强大的功能，旨在简化 AI 工作流程的创建和管理。流程允许开发人员高效地组合和协调编码任务和团队（Crews），为构建复杂的 AI 自动化提供了一个强大的框架。

流程允许您创建结构化的、事件驱动的工作流程。它们提供了一种无缝的方式来连接多个任务、管理状态并控制 AI 应用程序中的执行流程。通过流程，您可以轻松设计和实现多步骤流程，充分利用 CrewAI 的潜力。

1. **简化工作流程创建**：轻松地将多个团队和任务链接在一起，创建复杂的 AI 工作流程。

2. **状态管理**：流程使得在工作流程的不同任务之间管理和共享状态变得非常容易。

3. **事件驱动架构**：基于事件驱动模型构建，支持动态和响应式的工作流程。

4. **灵活的控制流**：在工作流程中实现条件逻辑、循环和分支。

## 入门指南

让我们创建一个简单的流程，在一个任务中使用 OpenAI 生成一个随机城市，然后在另一个任务中使用该城市生成一个有趣的事实。

```python Code theme={null}

from crewai.flow.flow import Flow, listen, start
from dotenv import load_dotenv
from litellm import completion


class ExampleFlow(Flow):
    model = "gpt-4o-mini"

    @start()
    def generate_city(self):
        print("启动流程")
        # 每个流程状态自动获得一个唯一 ID
        print(f"流程状态 ID: {self.state['id']}")

        response = completion(
            model=self.model,
            messages=[
                {
                    "role": "user",
                    "content": "返回世界上一个随机城市的名称。",
                },
            ],
        )

        random_city = response["choices"][0]["message"]["content"]
        # 将城市存储在我们的状态中
        self.state["city"] = random_city
        print(f"随机城市: {random_city}")

        return random_city

    @listen(generate_city)
    def generate_fun_fact(self, random_city):
        response = completion(
            model=self.model,
            messages=[
                {
                    "role": "user",
                    "content": f"告诉我一个关于 {random_city} 的有趣事实",
                },
            ],
        )

        fun_fact = response["choices"][0]["message"]["content"]
        # 将有趣的事实存储在我们的状态中
        self.state["fun_fact"] = fun_fact
        return fun_fact



flow = ExampleFlow()
flow.plot()
result = flow.kickoff()

print(f"生成的有趣事实: {result}")
```

<img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-1.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=18b381277b7b017abf7cb19bc5e03923" alt="流程可视化图像" data-og-width="1913" width="1913" data-og-height="989" height="989" data-path="images/crewai-flow-1.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-1.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=78864d97e0fc7f225a5313c9fb650900 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-1.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=3d87938c680e7aa201798075fe19dcf8 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-1.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=36448790f7ca45e69ffdd3ceb2b2e713 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-1.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=4d10a3f4f9ea1c9b0428fbb66f0fca17 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-1.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=928a75232235b73e9308d4d9cfeaf0e8 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-1.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=fa7022034285d0022ff07f97f6b675f7 2500w" />
在上面的例子中，我们创建了一个简单的流程，使用 OpenAI 生成一个随机城市，然后生成一个关于该城市的有趣事实。该流程由两个任务组成：`generate_city` 和 `generate_fun_fact`。`generate_city` 任务是流程的起点，`generate_fun_fact` 任务监听 `generate_city` 任务的输出。

每个流程实例在其状态中自动接收一个唯一标识符（UUID），这有助于跟踪和管理流程执行。状态还可以存储在流程执行过程中持续存在的附加数据（如生成的城市和有趣的事实）。

当您运行流程时，它将：

1. 为流程状态生成一个唯一 ID
2. 生成一个随机城市并将其存储在状态中
3. 生成一个关于该城市的有趣事实并将其存储在状态中
4. 将结果打印到控制台

状态的唯一 ID 和存储的数据可用于跟踪流程执行并在任务之间保持上下文。

**注意**：确保您已设置 `.env` 文件来存储您的 `OPENAI_API_KEY`。此密钥是向 OpenAI API 进行请求认证所必需的。

### @start()

`@start()` 装饰器标记流程的入口点。您可以：

* 声明多个无条件起点：`@start()`
* 基于先前的方法或路由器标签限制起点：`@start("method_or_label")`
* 提供可调用条件来控制何时应触发起点

所有满足条件的 `@start()` 方法将在流程开始或恢复时执行（通常是并行的）。

### @listen()

`@listen()` 装饰器用于将方法标记为流程中另一个任务输出的监听器。使用 `@listen()` 装饰的方法将在指定任务发出输出时执行。该方法可以访问其监听的任务的输出作为参数。

#### 用法

`@listen()` 装饰器可以以多种方式使用：

1. **按方法名称监听**：您可以传递要监听的方法名称作为字符串。当该方法完成时，监听器方法将被触发。

   ```python Code theme={null}
   @listen("generate_city")
   def generate_fun_fact(self, random_city):
       # 实现
   ```

2. **直接监听方法**：您可以传递方法本身。当该方法完成时，监听器方法将被触发。
   ```python Code theme={null}
   @listen(generate_city)
   def generate_fun_fact(self, random_city):
       # 实现
   ```

### 流程输出

访问和处理流程的输出对于将 AI 工作流程集成到更大的应用程序或系统中至关重要。CrewAI 流程提供了简单的机制来检索最终输出、访问中间结果和管理流程的总体状态。

#### 检索最终输出

当您运行流程时，最终输出由最后一个完成的方法确定。`kickoff()` 方法返回此最终方法的输出。

以下是如何访问最终输出的方法：

<CodeGroup>
  ```python Code theme={null}
  from crewai.flow.flow import Flow, listen, start

  class OutputExampleFlow(Flow):
      @start()
      def first_method(self):
          return "来自 first_method 的输出"

      @listen(first_method)
      def second_method(self, first_output):
          return f"第二方法接收到: {first_output}"


  flow = OutputExampleFlow()
  flow.plot("my_flow_plot")
  final_output = flow.kickoff()

  print("---- 最终输出 ----")
  print(final_output)
  ```

  ```text Output theme={null}
  ---- 最终输出 ----
  第二方法接收到: 来自 first_method 的输出
  ```
</CodeGroup>

<img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-2.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=3d987994d2c99a06a3cf149c71831fd5" alt="流程可视化图像" data-og-width="2015" width="2015" data-og-height="1040" height="1040" data-path="images/crewai-flow-2.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-2.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=e6b4e913cd2d4bf4dc67bdcb2e59cceb 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-2.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=245303e4f6e5bc30819aa9357561e7b3 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-2.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=32155410f336267e29c64407e22ae57e 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-2.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=5dc414bc338e0475ae40aa3eedea0bd8 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-2.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=cfdf47937eb1f0a1f7e9ffdaab866e5a 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-2.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=ef7d71c39b8ea4ad865c514420df28d1 2500w" />

在这个例子中，`second_method` 是最后一个完成的方法，因此它的输出将是流程的最终输出。
`kickoff()` 方法将返回最终输出，然后将其打印到控制台。`plot()` 方法将生成 HTML 文件，这将帮助您理解流程。

#### 访问和更新状态

除了检索最终输出外，您还可以在流程中访问和更新状态。状态可用于在流程中的不同方法之间存储和共享数据。流程运行后，您可以访问状态以检索在执行过程中添加或更新的任何信息。

以下是更新和访问状态的示例：

<CodeGroup>
  ```python Code theme={null}
  from crewai.flow.flow import Flow, listen, start
  from pydantic import BaseModel

  class ExampleState(BaseModel):
      counter: int = 0
      message: str = ""

  class StateExampleFlow(Flow[ExampleState]):

      @start()
      def first_method(self):
          self.state.message = "来自 first_method 的问候"
          self.state.counter += 1

      @listen(first_method)
      def second_method(self):
          self.state.message += " - 被 second_method 更新"
          self.state.counter += 1
          return self.state.message

  flow = StateExampleFlow()
  flow.plot("my_flow_plot")
  final_output = flow.kickoff()
  print(f"最终输出: {final_output}")
  print("最终状态:")
  print(flow.state)
  ```

  ```text Output theme={null}
  最终输出: 来自 first_method 的问候 - 被 second_method 更新
  最终状态:
  counter=2 message='来自 first_method 的问候 - 被 second_method 更新'
  ```
</CodeGroup>

<img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-2.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=3d987994d2c99a06a3cf149c71831fd5" alt="流程可视化图像" data-og-width="2015" width="2015" data-og-height="1040" height="1040" data-path="images/crewai-flow-2.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-2.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=e6b4e913cd2d4bf4dc67bdcb2e59cceb 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-2.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=245303e4f6e5bc30819aa9357561e7b3 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-2.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=32155410f336267e29c64407e22ae57e 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-2.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=5dc414bc338e0475ae40aa3eedea0bd8 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-2.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=cfdf47937eb1f0a1f7e9ffdaab866e5a 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-2.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=ef7d71c39b8ea4ad865c514420df28d1 2500w" />

在这个例子中，状态被 `first_method` 和 `second_method` 更新。
流程运行后，您可以访问最终状态以查看这些方法所做的更新。

通过确保最终方法的输出被返回并提供对状态的访问，CrewAI 流程使得将 AI 工作流程的结果集成到更大的应用程序或系统中变得容易，
同时在流程执行过程中维护和访问状态。

## 流程状态管理

有效管理状态对于构建可靠且可维护的 AI 工作流程至关重要。CrewAI 流程为非结构化和结构化状态管理提供了强大的机制，
允许开发人员选择最适合其应用程序需求的方法。

### 非结构化状态管理

在非结构化状态管理中，所有状态都存储在 `Flow` 类的 `state` 属性中。
这种方法提供了灵活性，使开发人员能够动态添加或修改状态属性，而无需定义严格的模式。
即使是非结构化状态，CrewAI 流程也会自动为每个状态实例生成并维护一个唯一标识符（UUID）。

```python Code theme={null}
from crewai.flow.flow import Flow, listen, start

class UnstructuredExampleFlow(Flow):

    @start()
    def first_method(self):
        # 状态自动包含一个 'id' 字段
        print(f"状态 ID: {self.state['id']}")
        self.state['counter'] = 0
        self.state['message'] = "来自结构化流程的问候"

    @listen(first_method)
    def second_method(self):
        self.state['counter'] += 1
        self.state['message'] += " - 已更新"

    @listen(second_method)
    def third_method(self):
        self.state['counter'] += 1
        self.state['message'] += " - 再次更新"

        print(f"third_method 后的状态: {self.state}")


flow = UnstructuredExampleFlow()
flow.plot("my_flow_plot")
flow.kickoff()
```

<img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-3.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=1d64a80a490430f29b7fa1085a3062c4" alt="流程可视化图像" data-og-width="1974" width="1974" data-og-height="1058" height="1058" data-path="images/crewai-flow-3.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-3.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=192f7a8605d3a5c12b6b61aa4a23917f 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-3.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=f41cbc9a268ba4bbb466fa2e2a1c2c1e 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-3.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=4d2315a6e69d8125e7e144f04180529f 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-3.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=02eb5ffde3ef5936b2cf172160c72f72 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-3.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=3fc5bb51802a4a5d641834e19d24e565 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-3.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=85414106ada4d15dcb7bccc086194b84 2500w" />

**注意**：`id` 字段是自动生成并在整个流程执行过程中保留的。您不需要手动管理或设置它，即使使用新数据更新状态，它也会被维护。

**关键点**：

* **灵活性**：您可以动态地向 `self.state` 添加属性，而无需预定义约束。
* **简单性**：适用于状态结构简单或变化很大的简单工作流程。

### 结构化状态管理

结构化状态管理利用预定义的模式来确保工作流程中的一致性和类型安全。
通过使用像 Pydantic 的 `BaseModel` 这样的模型，开发人员可以定义状态的确切形状，实现更好的验证和开发环境中的自动完成功能。

CrewAI 流程中的每个状态都会自动接收一个唯一标识符（UUID），以帮助跟踪和管理状态实例。此 ID 由流程系统自动生成和管理。

```python Code theme={null}
from crewai.flow.flow import Flow, listen, start
from pydantic import BaseModel


class ExampleState(BaseModel):
    # 注意：'id' 字段会自动添加到所有状态中
    counter: int = 0
    message: str = ""


class StructuredExampleFlow(Flow[ExampleState]):

    @start()
    def first_method(self):
        # 如果需要，可以访问自动生成的 ID
        print(f"状态 ID: {self.state.id}")
        self.state.message = "来自结构化流程的问候"

    @listen(first_method)
    def second_method(self):
        self.state.counter += 1
        self.state.message += " - 已更新"

    @listen(second_method)
    def third_method(self):
        self.state.counter += 1
        self.state.message += " - 再次更新"

        print(f"third_method 后的状态: {self.state}")


flow = StructuredExampleFlow()
flow.kickoff()
```

<img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-3.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=1d64a80a490430f29b7fa1085a3062c4" alt="流程可视化图像" data-og-width="1974" width="1974" data-og-height="1058" height="1058" data-path="images/crewai-flow-3.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-3.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=192f7a8605d3a5c12b6b61aa4a23917f 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-3.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=f41cbc9a268ba4bbb466fa2e2a1c2c1e 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-3.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=4d2315a6e69d8125e7e144f04180529f 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-3.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=02eb5ffde3ef5936b2cf172160c72f72 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-3.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=3fc5bb51802a4a5d641834e19d24e565 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-3.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=85414106ada4d15dcb7bccc086194b84 2500w" />

**关键点**：

* **定义的模式**：`ExampleState` 清晰地概述了状态结构，增强了代码的可读性和可维护性。
* **类型安全**：利用 Pydantic 确保状态属性符合指定的类型，减少运行时错误。
* **自动完成**：IDE 可以基于定义的状态模型提供更好的自动完成和错误检查。

### 在非结构化和结构化状态管理之间选择

* **在以下情况下使用非结构化状态管理**：

  * 工作流程的状态简单或高度动态。
  * 优先考虑灵活性而不是严格的状态定义。
  * 需要快速原型设计，而没有定义模式的开销。

* **在以下情况下使用结构化状态管理**：
  * 工作流程需要定义明确且一致的状态结构。
  * 类型安全和验证对应用程序的可靠性很重要。
  * 您希望利用 IDE 功能，如自动完成和类型检查，以获得更好的开发体验。

通过提供非结构化和结构化状态管理选项，CrewAI 流程使开发人员能够构建既灵活又强大的 AI 工作流程，满足广泛的应用程序需求。

## 流程持久化

@persist 装饰器在 CrewAI 流程中启用自动状态持久化，允许您在重启或不同的工作流程执行之间保持流程状态。此装饰器可以在类级别或方法级别应用，提供了在管理状态持久化方面的灵活性。

### 类级别持久化

在类级别应用时，@persist 装饰器自动持久化所有流程方法状态：

```python  theme={null}
@persist  # 默认使用 SQLiteFlowPersistence
class MyFlow(Flow[MyState]):
    @start()
    def initialize_flow(self):
        # 此方法将自动具有其状态持久化
        self.state.counter = 1
        print("已初始化流程。状态 ID:", self.state.id)

    @listen(initialize_flow)
    def next_step(self):
        # 状态（包括 self.state.id）自动重新加载
        self.state.counter += 1
        print("流程状态已持久化。计数器:", self.state.counter)
```

### 方法级别持久化

对于更细粒度的控制，您可以将 @persist 应用于特定方法：

```python  theme={null}
class AnotherFlow(Flow[dict]):
    @persist  # 仅持久化此方法的状态
    @start()
    def begin(self):
        if "runs" not in self.state:
            self.state["runs"] = 0
        self.state["runs"] += 1
        print("方法级别持久化的运行次数:", self.state["runs"])
```

### 工作原理

1. **唯一状态标识**
   * 每个流程状态自动接收一个唯一的 UUID
   * ID 在状态更新和方法调用之间保留
   * 支持结构化（Pydantic BaseModel）和非结构化（字典）状态

2. **默认 SQLite 后端**
   * SQLiteFlowPersistence 是默认的存储后端
   * 状态自动保存到本地 SQLite 数据库
   * 强大的错误处理确保如果数据库操作失败，会显示清晰的错误消息

3. **错误处理**
   * 数据库操作的综合错误消息
   * 保存和加载期间的状态自动验证
   * 持久化操作遇到问题时的清晰反馈

### 重要考虑因素

* **状态类型**：支持结构化（Pydantic BaseModel）和非结构化（字典）状态
* **自动 ID**：如果不存在，`id` 字段会自动添加
* **状态恢复**：失败或重启的流程可以自动重新加载其先前的状态
* **自定义实现**：您可以提供自己的 FlowPersistence 实现，以满足专门的存储需求

### 技术优势

1. **通过低级访问实现精确控制**
   * 直接访问持久化操作，适用于高级用例
   * 通过方法级别持久化装饰器实现细粒度控制
   * 内置状态检查和调试功能
   * 完全可见状态更改和持久化操作

2. **增强的可靠性**
   * 系统故障或重启后的自动状态恢复
   * 基于事务的状态更新以确保数据完整性
   * 具有清晰错误消息的综合错误处理
   * 保存和加载操作期间的强大验证

3. **可扩展架构**
   * 通过 FlowPersistence 接口自定义持久化后端
   * 支持除 SQLite 之外的专门存储解决方案
   * 兼容结构化（Pydantic）和非结构化（dict）状态
   * 与现有 CrewAI 流程模式无缝集成

持久化系统的架构强调技术精确性和自定义选项，允许开发人员在保持完全状态管理控制的同时受益于内置的可靠性功能。

## 流程控制

### 条件逻辑：`or`

流程中的 `or_` 函数允许您监听多个方法，并在任何指定方法发出输出时触发监听器方法。

<CodeGroup>
  ```python Code theme={null}
  from crewai.flow.flow import Flow, listen, or_, start

  class OrExampleFlow(Flow):

      @start()
      def start_method(self):
          return "来自 start 方法的问候"

      @listen(start_method)
      def second_method(self):
          return "来自 second 方法的问候"

      @listen(or_(start_method, second_method))
      def logger(self, result):
          print(f"日志记录器: {result}")



  flow = OrExampleFlow()
  flow.plot("my_flow_plot")
  flow.kickoff()
  ```

  ```text Output theme={null}
  日志记录器: 来自 start 方法的问候
  日志记录器: 来自 second 方法的问候
  ```
</CodeGroup>

<img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-4.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=88ce9c9f10781b835f170847bc541a13" alt="流程可视化图像" data-og-width="2026" width="2026" data-og-height="1016" height="1016" data-path="images/crewai-flow-4.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-4.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=796ce622251faa461b481eb5d7cdcf70 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-4.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=260fcd89a5b3a6a42a25dd4f41e7c5c6 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-4.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=b9268adb3abef93c7cce693a424a78ba 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-4.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=75f3ad392bfd6b72bd29d701675899d6 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-4.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=dd771250338648e1f22c1463cb8e2ff0 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-4.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=b8e6fd63ec2ba23d9fa4f1dc2fd87143 2500w" />

当您运行此流程时，`logger` 方法将由 `start_method` 或 `second_method` 的输出触发。
`or_` 函数用于监听多个方法，并在任何指定方法发出输出时触发监听器方法。

### 条件逻辑：`and`

流程中的 `and_` 函数允许您监听多个方法，并且仅在所有指定方法发出输出时才触发监听器方法。

<CodeGroup>
  ```python Code theme={null}
  from crewai.flow.flow import Flow, and_, listen, start

  class AndExampleFlow(Flow):

      @start()
      def start_method(self):
          self.state["greeting"] = "来自 start 方法的问候"

      @listen(start_method)
      def second_method(self):
          self.state["joke"] = "电脑吃什么？微芯片。"

      @listen(and_(start_method, second_method))
      def logger(self):
          print("---- 日志记录器 ----")
          print(self.state)

  flow = AndExampleFlow()
  flow.plot()
  flow.kickoff()
  ```

  ```text Output theme={null}
  ---- 日志记录器 ----
  {'greeting': '来自 start 方法的问候', 'joke': '电脑吃什么？微芯片。'}
  ```
</CodeGroup>

<img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-5.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=104318219be9d3502ac57ebb513aded7" alt="流程可视化图像" data-og-width="2062" width="2062" data-og-height="987" height="987" data-path="images/crewai-flow-5.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-5.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=6e9cb9d2b1ec2cb2aee2df008d3696c9 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-5.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=07cbcc6de6e8c8ae5da6c02a6fe4b457 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-5.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=afc6aad8f7276be4918527e553b5aa81 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-5.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=e026919d9dff7ce0b0e592f4f2c0c4fd 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-5.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=03ea50c8681de2b8ea8cada6c0150e2c 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-5.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=61f9c7b0aceb69df6346ab2af321b779 2500w" />

当您运行此流程时，`logger` 方法仅在 `start_method` 和 `second_method` 都发出输出时才会触发。
`and_` 函数用于监听多个方法，并且仅在所有指定方法发出输出时才触发监听器方法。

### 路由器

流程中的 `@router()` 装饰器允许您基于方法的输出定义条件路由逻辑。
您可以根据方法的输出指定不同的路由，从而动态控制执行流程。

<CodeGroup>
  ```python Code theme={null}
  import random
  from crewai.flow.flow import Flow, listen, router, start
  from pydantic import BaseModel

  class ExampleState(BaseModel):
      success_flag: bool = False

  class RouterFlow(Flow[ExampleState]):

      @start()
      def start_method(self):
          print("启动结构化流程")
          random_boolean = random.choice([True, False])
          self.state.success_flag = random_boolean

      @router(start_method)
      def second_method(self):
          if self.state.success_flag:
              return "success"
          else:
              return "failed"

      @listen("success")
      def third_method(self):
          print("第三方法运行中")

      @listen("failed")
      def fourth_method(self):
          print("第四方法运行中")


  flow = RouterFlow()
  flow.plot("my_flow_plot")
  flow.kickoff()
  ```

  ```text Output theme={null}
  启动结构化流程
  第三方法运行中
  第四方法运行中
  ```
</CodeGroup>

<img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-6.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=f8cad73f073b4e936ef68d88545f1777" alt="流程可视化图像" data-og-width="1951" width="1951" data-og-height="1101" height="1101" data-path="images/crewai-flow-6.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-6.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=9a8462f42a9d9e14748d35312553ec6c 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-6.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=78e66eabae15099e2ef1d0c314d3cb04 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-6.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=a144377de810ed24f1d1aed1ba54d2d7 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-6.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=2b17ebb2dd4eee4d086a8d0126a36c0d 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-6.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=4536b83aa7ff7e897f1193709ace944f 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-6.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=3b89cff817f7b4d05338f4a3a028f974 2500w" />

在上面的例子中，`start_method` 生成一个随机布尔值并将其设置在状态中。
`second_method` 使用 `@router()` 装饰器基于布尔值定义条件路由逻辑。
如果布尔值为 `True`，则方法返回 `"success"`，如果为 `False`，则方法返回 `"failed"`。
`third_method` 和 `fourth_method` 监听 `second_method` 的输出并根据返回值执行。

当您运行此流程时，输出将根据 `start_method` 生成的随机布尔值而变化。

## 向流程添加代理

代理可以无缝集成到您的流程中，在您需要更简单、专注的任务执行时，提供完整团队（Crews）的轻量级替代方案。以下是如何在流程中使用代理执行市场研究的示例：

```python  theme={null}
import asyncio
from typing import Any, Dict, List

from crewai_tools import SerperDevTool
from pydantic import BaseModel, Field

from crewai.agent import Agent
from crewai.flow.flow import Flow, listen, start


# 定义结构化输出格式
class MarketAnalysis(BaseModel):
    key_trends: List[str] = Field(description="识别的市场趋势列表")
    market_size: str = Field(description="估计的市场规模")
    competitors: List[str] = Field(description="该领域的主要竞争对手")


# 定义流程状态
class MarketResearchState(BaseModel):
    product: str = ""
    analysis: MarketAnalysis | None = None


# 创建流程类
class MarketResearchFlow(Flow[MarketResearchState]):
    @start()
    def initialize_research(self) -> Dict[str, Any]:
        print(f"开始为 {self.state.product} 进行市场研究")
        return {"product": self.state.product}

    @listen(initialize_research)
    async def analyze_market(self) -> Dict[str, Any]:
        # 创建市场研究代理
        analyst = Agent(
            role="市场研究分析师",
            goal=f"分析 {self.state.product} 的市场",
            backstory="您是一位经验丰富的市场分析师，具有识别市场趋势和机会的专业知识。",
            tools=[SerperDevTool()],
            verbose=True,
        )

        # 定义研究查询
        query = f"""
        研究 {self.state.product} 的市场。包括：
        1. 主要市场趋势
        2. 市场规模
        3. 主要竞争对手

        根据指定的结构格式化您的回答。
        """

        # 使用结构化输出格式执行分析
        result = await analyst.kickoff_async(query, response_format=MarketAnalysis)
        if result.pydantic:
            print("结果", result.pydantic)
        else:
            print("结果", result)

        # 返回分析以更新状态
        return {"analysis": result.pydantic}

    @listen(analyze_market)
    def present_results(self, analysis) -> None:
        print("\n市场分析结果")
        print("=====================")

        if isinstance(analysis, dict):
            # 如果我们得到带有 'analysis' 键的字典，则提取实际的分析对象
            market_analysis = analysis.get("analysis")
        else:
            market_analysis = analysis

        if market_analysis and isinstance(market_analysis, MarketAnalysis):
            print("\n主要市场趋势：")
            for trend in market_analysis.key_trends:
                print(f"- {trend}")

            print(f"\n市场规模: {market_analysis.market_size}")

            print("\n主要竞争对手：")
            for competitor in market_analysis.competitors:
                print(f"- {competitor}")
        else:
            print("没有结构化分析数据可用。")
            print("原始分析:", analysis)


# 使用示例
async def run_flow():
    flow = MarketResearchFlow()
    flow.plot("MarketResearchFlowPlot")
    result = await flow.kickoff_async(inputs={"product": "AI 驱动的聊天机器人"})
    return result


# 运行流程
if __name__ == "__main__":
    asyncio.run(run_flow())
```

<img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-7.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=6c60457e1a2b9bc0ef957c373a88359b" alt="流程可视化图像" data-og-width="1933" width="1933" data-og-height="959" height="959" data-path="images/crewai-flow-7.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-7.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=4e6a743b2b19cd86dadbbd015d0a0393 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-7.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=740f254bb03d60cd011911dab702ca77 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-7.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=b93c5bde69019cdc34c143bcc0885743 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-7.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=4eabe8d3536d6588a14157b60bc7a1e0 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-7.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=e76b5df7821722a59d3267f3a0eff3ed 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-7.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=3a9775e3f5798ccb73e5feb7e53319fd 2500w" />

这个示例展示了在流程中使用代理的几个关键功能：

1. **结构化输出**：使用 Pydantic 模型定义预期的输出格式（`MarketAnalysis`），确保整个流程中的类型安全和结构化数据。

2. **状态管理**：流程状态（`MarketResearchState`）在步骤之间维护上下文并存储输入和输出。

3. **工具集成**：代理可以使用工具（如 `WebsiteSearchTool`）来增强其功能。

## 向流程添加团队

在 CrewAI 中创建包含多个团队的流程非常简单。

您可以通过运行以下命令生成一个新的 CrewAI 项目，其中包含创建包含多个团队的流程所需的所有脚手架：

```bash  theme={null}
crewai create flow name_of_flow
```

此命令将生成一个新的 CrewAI 项目，具有必要的文件夹结构。生成的项目包括一个名为 `poem_crew` 的预构建团队，该团队已经可以工作。您可以通过复制、粘贴和编辑此团队作为模板来创建其他团队。

### 文件夹结构

运行 `crewai create flow name_of_flow` 命令后，您将看到类似于以下的文件夹结构：

| 目录/文件           | 描述                                                         |
| :------------------- | :---------------------------------------------------------- |
| `name_of_flow/`      | 流程的根目录。                                               |
| ├── `crews/`         | 包含特定团队的目录。                                         |
| │ └── `poem_crew/`   | "poem_crew" 的目录，包含其配置和脚本。                        |
| │ ├── `config/`      | "poem_crew" 的配置文件目录。                                 |
| │ │ ├── `agents.yaml` | 为 "poem_crew" 定义代理的 YAML 文件。                         |
| │ │ └── `tasks.yaml`  | 为 "poem_crew" 定义任务的 YAML 文件。                          |
| │ ├── `poem_crew.py` | "poem_crew" 功能的脚本。                                     |
| ├── `tools/`         | 用于流程中附加工具的目录。                                   |
| │ └── `custom_tool.py` | 自定义工具实现。                                              |
| ├── `main.py`        | 用于运行流程的主脚本。                                       |
| ├── `README.md`      | 项目描述和说明。                                             |
| ├── `pyproject.toml` | 用于项目依赖项和设置的配置文件。                             |
| └── `.gitignore`     | 指定版本控制中要忽略的文件和目录。                           |

### 构建您的团队

在 `crews` 文件夹中，您可以定义多个团队。每个团队都有自己的文件夹，包含配置文件和团队定义文件。例如，`poem_crew` 文件夹包含：

* `config/agents.yaml`：为团队定义代理。
* `config/tasks.yaml`：为团队定义任务。
* `poem_crew.py`：包含团队定义，包括代理、任务和团队本身。

您可以复制、粘贴和编辑 `poem_crew` 来创建其他团队。

### 在 `main.py` 中连接团队

`main.py` 文件是您创建流程并将团队连接在一起的地方。您可以使用 `Flow` 类和装饰器 `@start` 和 `@listen` 来定义流程。

以下是在 `main.py` 文件中连接 `poem_crew` 的示例：

```python Code theme={null}
#!/usr/bin/env python
from random import randint

from pydantic import BaseModel
from crewai.flow.flow import Flow, listen, start
from .crews.poem_crew.poem_crew import PoemCrew

class PoemState(BaseModel):
    sentence_count: int = 1
    poem: str = ""

class PoemFlow(Flow[PoemState]):

    @start()
    def generate_sentence_count(self):
        print("生成句子计数")
        self.state.sentence_count = randint(1, 5)

    @listen(generate_sentence_count)
    def generate_poem(self):
        print("生成诗歌")
        result = PoemCrew().crew().kickoff(inputs={"sentence_count": self.state.sentence_count})

        print("诗歌已生成", result.raw)
        self.state.poem = result.raw

    @listen(generate_poem)
    def save_poem(self):
        print("保存诗歌")
        with open("poem.txt", "w") as f:
            f.write(self.state.poem)

def kickoff():
    poem_flow = PoemFlow()
    poem_flow.kickoff()


def plot():
    poem_flow = PoemFlow()
    poem_flow.plot("PoemFlowPlot")

if __name__ == "__main__":
    kickoff()
    plot()
```

在这个例子中，`PoemFlow` 类定义了一个流程，该流程生成句子计数，使用 `PoemCrew` 生成诗歌，然后将诗歌保存到文件中。通过调用 `kickoff()` 方法启动流程。PoemFlowPlot 将由 `plot()` 方法生成。

<img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-8.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=5321ca5d1f3c662dc7cff6950ba46000" alt="流程可视化图像" data-og-width="1901" width="1901" data-og-height="1032" height="1032" data-path="images/crewai-flow-8.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-8.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=5179beeb8c5b02eafdc1fce722004529 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-8.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=994b487041be812c1df343b23b5da9f2 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-8.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=244859a74e398490fa313beb91a3b9a7 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-8.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=7ab59d8b176f60f2ba882eca41100ce1 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-8.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=c817c66b2d016c0b1bc203c32413d08e 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/crewai-flow-8.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=f5936902cb8e9e405a65ab3e1adbbd43 2500w" />

### 运行流程

（可选）在运行流程之前，您可以通过运行以下命令安装依赖项：

```bash  theme={null}
crewai install
```

安装完所有依赖项后，您需要通过运行以下命令激活虚拟环境：

```bash  theme={null}
source .venv/bin/activate
```

激活虚拟环境后，您可以通过执行以下命令之一来运行流程：

```bash  theme={null}
crewai flow kickoff
```

或

```bash  theme={null}
uv run kickoff
```

流程将执行，您应该在控制台中看到输出。

## 绘制流程图

可视化您的 AI 工作流程可以为您提供有关流程结构和执行路径的宝贵见解。CrewAI 提供了一个强大的可视化工具，允许您生成流程的交互式图表，使理解和优化 AI 工作流程变得更加容易。

### 什么是流程图？

CrewAI 中的流程图是 AI 工作流程的图形表示。它们显示各种任务、它们之间的连接以及任务之间的数据流。这种可视化有助于理解操作顺序、识别瓶颈，并确保工作流程逻辑与您的期望一致。

### 如何生成流程图

CrewAI 提供了两种便捷的方法来生成流程图：

#### 选项 1：使用 `plot()` 方法

如果您直接使用流程实例，可以通过在流程对象上调用 `plot()` 方法来生成流程图。此方法将创建一个包含流程交互式图表的 HTML 文件。

```python Code theme={null}
# 假设您有一个流程实例
flow.plot("my_flow_plot")
```

这将在当前目录中生成一个名为 `my_flow_plot.html` 的文件。您可以在 Web 浏览器中打开此文件以查看交互式图表。

#### 选项 2：使用命令行

如果您在结构化的 CrewAI 项目中工作，可以使用命令行生成流程图。这对于较大的项目特别有用，在这些项目中您希望可视化整个流程设置。

```bash  theme={null}
crewai flow plot
```

此命令将生成流程图的 HTML 文件，类似于 `plot()` 方法。文件将保存在您的项目目录中，您可以在 Web 浏览器中打开它以探索流程。

### 理解流程图

生成的流程图将显示代表流程中任务的节点，以及指示执行流程的有向边。该图是交互式的，允许您放大和缩小，并将鼠标悬停在节点上以查看其他详细信息。

通过可视化您的流程，您可以更清楚地了解工作流程的结构，使其更容易调试、优化和向他人展示您的 AI 流程。

### 结论

绘制流程图是 CrewAI 的一个强大功能，增强了您设计和管理复杂 AI 工作流程的能力。无论您选择使用 `plot()` 方法还是命令行，生成流程图都将为您提供工作流程的可视化表示，有助于开发和演示。

## 后续步骤

如果您有兴趣探索更多流程示例，我们的示例存储库中有各种推荐。以下是四个特定的流程示例，每个都展示独特的用例，帮助您将当前问题类型与特定示例匹配：

1. **邮件自动回复流程**：此示例展示了一个无限循环，其中后台作业持续运行以自动回复邮件。这是需要重复执行任务而无需手动干预的绝佳用例。[查看示例](https://github.com/crewAIInc/crewAI-examples/tree/main/email_auto_responder_flow)

2. **潜在客户评分流程**：此流程展示了添加人工反馈循环以及使用路由器处理不同的条件分支。这是一个很好的示例，展示了如何在工作流程中集成动态决策和人工监督。[查看示例](https://github.com/crewAIInc/crewAI-examples/tree/main/lead-score-flow)

3. **写书流程**：此示例擅长将多个团队链接在一起，其中一个团队的输出被另一个团队使用。具体来说，一个团队概述整本书，另一个团队基于大纲生成章节。最终，所有内容连接起来产生一本完整的书。此流程适用于需要不同任务之间协调的复杂、多步骤流程。[查看示例](https://github.com/crewAIInc/crewAI-examples/tree/main/write_a_book_with_flows)

4. **会议助手流程**：此流程展示了如何广播一个事件以触发多个后续操作。例如，会议完成后，流程可以更新 Trello 板、发送 Slack 消息并保存结果。这是处理单个事件的多个结果的绝佳示例，非常适合全面的任务管理和通知系统。[查看示例](https://github.com/crewAIInc/crewAI-examples/tree/main/meeting_assistant_flow)

通过探索这些示例，您可以了解如何利用 CrewAI 流程处理各种用例，从自动化重复任务到管理具有动态决策和人工反馈的复杂、多步骤流程。

另外，请查看我们关于如何在 CrewAI 中使用流程的 YouTube 视频！

<iframe className="w-full aspect-video rounded-xl" src="https://www.youtube.com/embed/MTb5my6VOT8" title="CrewAI 流程概述" frameBorder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerPolicy="strict-origin-when-cross-origin" allowFullScreen />

## 运行流程

有两种运行流程的方法：

### 使用流程 API

您可以通过创建流程类的实例并调用 `kickoff()` 方法来以编程方式运行流程：

```python  theme={null}
flow = ExampleFlow()
result = flow.kickoff()
```

### 使用 CLI

从版本 0.103.0 开始，您可以使用 `crewai run` 命令运行流程：

```shell  theme={null}
crewai run
```

此命令会自动检测您的项目是否是流程（基于 pyproject.toml 中的 `type = "flow"` 设置）并相应地运行它。这是从命令行运行流程的推荐方法。

为了向后兼容，您也可以使用：

```shell  theme={null}
crewai flow kickoff
```

但是，`crewai run` 命令现在是首选方法，因为它适用于团队和流程。