# 事件监听器

> 利用 CrewAI 事件来构建自定义集成和监控系统

## 概述

CrewAI 提供了一个强大的事件系统，允许您监听并响应 Crew 执行过程中发生的各种事件。此功能使您能够构建自定义集成、监控解决方案、日志系统，或任何需要基于 CrewAI 内部事件触发的其他功能。

## 工作原理

CrewAI 使用事件总线架构在整个执行生命周期中发出事件。事件系统建立在以下组件之上：

1. **CrewAIEventsBus**：管理事件注册和发出的单例事件总线
2. **BaseEvent**：系统中所有事件的基类
3. **BaseEventListener**：用于创建自定义事件监听器的抽象基类

当 CrewAI 中发生特定操作时（如 Crew 开始执行、Agent 完成任务或工具被使用），系统会发出相应的事件。您可以为这些事件注册处理程序，在它们发生时执行自定义代码。

<Note type="info" title="企业增强功能：提示追踪">
  CrewAI AMP 提供了一个内置的提示追踪功能，利用事件系统来跟踪、存储和可视化所有提示、补全及相关元数据。这为您的代理操作提供了强大的调试功能和透明度。

    <img src="https://mintcdn.com/crewai/Tp3HEbbp9mp-dy3H/images/enterprise/traces-overview.png?fit=max&auto=format&n=Tp3HEbbp9mp-dy3H&q=85&s=9c02d5b7306bf7adaeadd77a018f8fea" alt="Prompt Tracing Dashboard" data-og-width="2244" width="2244" data-og-height="1422" height="1422" data-path="images/enterprise/traces-overview.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/Tp3HEbbp9mp-dy3H/images/enterprise/traces-overview.png?w=280&fit=max&auto=format&n=Tp3HEbbp9mp-dy3H&q=85&s=e66e7c56a8848b69266563ea8cddfc4e 280w, https://mintcdn.com/crewai/Tp3HEbbp9mp-dy3H/images/enterprise/traces-overview.png?w=560&fit=max&auto=format&n=Tp3HEbbp9mp-dy3H&q=85&s=f590b3901aaa5994042c79426d78bd6c 560w, https://mintcdn.com/crewai/Tp3HEbbp9mp-dy3H/images/enterprise/traces-overview.png?w=840&fit=max&auto=format&n=Tp3HEbbp9mp-dy3H&q=85&s=0ecb9dcb307e8f130f53393bd3abc12d 840w, https://mintcdn.com/crewai/Tp3HEbbp9mp-dy3H/images/enterprise/traces-overview.png?w=1100&fit=max&auto=format&n=Tp3HEbbp9mp-dy3H&q=85&s=5fc6fcfc51c4e8f4ce16d237228043d6 1100w, https://mintcdn.com/crewai/Tp3HEbbp9mp-dy3H/images/enterprise/traces-overview.png?w=1650&fit=max&auto=format&n=Tp3HEbbp9mp-dy3H&q=85&s=253eaed4ec34a35798dad42e9a388859 1650w, https://mintcdn.com/crewai/Tp3HEbbp9mp-dy3H/images/enterprise/traces-overview.png?w=2500&fit=max&auto=format&n=Tp3HEbbp9mp-dy3H&q=85&s=ec818e09bc20b3f72b1bcf1970804d13 2500w" />

  使用提示追踪，您可以：

  * 查看发送到您的 LLM 的所有提示的完整历史记录
  * 跟踪令牌使用情况和成本
  * 调试代理推理失败
  * 与您的团队共享提示序列
  * 比较不同的提示策略
  * 导出追踪以进行合规性和审计
</Note>

## 创建自定义事件监听器

要创建自定义事件监听器，您需要：

1. 创建一个继承自 `BaseEventListener` 的类
2. 实现 `setup_listeners` 方法
3. 为您感兴趣的事件注册处理程序
4. 在适当的文件中创建您的监听器实例

以下是自定义事件监听器类的简单示例：

```python  theme={null}
from crewai.events import (
    CrewKickoffStartedEvent,
    CrewKickoffCompletedEvent,
    AgentExecutionCompletedEvent,
)
from crewai.events import BaseEventListener

class MyCustomListener(BaseEventListener):
    def __init__(self):
        super().__init__()

    def setup_listeners(self, crewai_event_bus):
        @crewai_event_bus.on(CrewKickoffStartedEvent)
        def on_crew_started(source, event):
            print(f"Crew '{event.crew_name}' has started execution!")

        @crewai_event_bus.on(CrewKickoffCompletedEvent)
        def on_crew_completed(source, event):
            print(f"Crew '{event.crew_name}' has completed execution!")
            print(f"Output: {event.output}")

        @crewai_event_bus.on(AgentExecutionCompletedEvent)
        def on_agent_execution_completed(source, event):
            print(f"Agent '{event.agent.role}' completed task")
            print(f"Output: {event.output}")
```

## 正确注册您的监听器

仅仅定义您的监听器类是不够的。您需要创建它的实例，并确保它在您的应用程序中被导入。这确保了：

1. 事件处理程序已注册到事件总线
2. 监听器实例保留在内存中（不被垃圾回收）
3. 当事件发出时，监听器处于活动状态

### 选项 1：在您的 Crew 或 Flow 实现中导入和实例化

最重要的是在定义和执行 Crew 或 Flow 的文件中创建您的监听器实例：

#### 对于基于 Crew 的应用程序

在您的 Crew 实现文件的顶部创建并导入您的监听器：

```python  theme={null}
# 在您的 crew.py 文件中
from crewai import Agent, Crew, Task
from my_listeners import MyCustomListener

# 创建您的监听器实例
my_listener = MyCustomListener()

class MyCustomCrew:
    # 您的 crew 实现...

    def crew(self):
        return Crew(
            agents=[...],
            tasks=[...],
            # ...
        )
```

#### 对于基于 Flow 的应用程序

在您的 Flow 实现文件的顶部创建并导入您的监听器：

```python  theme={null}
# 在您的 main.py 或 flow.py 文件中
from crewai.flow import Flow, listen, start
from my_listeners import MyCustomListener

# 创建您的监听器实例
my_listener = MyCustomListener()

class MyCustomFlow(Flow):
    # 您的 flow 实现...

    @start()
    def first_step(self):
        # ...
```

这确保了当您的 Crew 或 Flow 执行时，您的监听器被加载并处于活动状态。

### 选项 2：为您的监听器创建一个包

对于更结构化的方法，特别是当您有多个监听器时：

1. 为您的监听器创建一个包：

```
my_project/
  ├── listeners/
  │   ├── __init__.py
  │   ├── my_custom_listener.py
  │   └── another_listener.py
```

2. 在 `my_custom_listener.py` 中，定义您的监听器类并创建一个实例：

```python  theme={null}
# my_custom_listener.py
from crewai.events import BaseEventListener
# ... 导入事件 ...

class MyCustomListener(BaseEventListener):
    # ... 实现 ...

# 创建您的监听器实例
my_custom_listener = MyCustomListener()
```

3. 在 `__init__.py` 中，导入监听器实例以确保它们被加载：

```python  theme={null}
# __init__.py
from .my_custom_listener import my_custom_listener
from .another_listener import another_listener

# 如果需要在其他地方访问它们，可选择导出
__all__ = ['my_custom_listener', 'another_listener']
```

4. 在您的 Crew 或 Flow 文件中导入您的监听器包：

```python  theme={null}
# 在您的 crew.py 或 flow.py 文件中
import my_project.listeners  # 这会加载您的所有监听器

class MyCustomCrew:
    # 您的 crew 实现...
```

这就是第三方事件监听器在 CrewAI 代码库中注册的方式。

## 可用事件类型

CrewAI 提供了多种您可以监听的事件：

### Crew 事件

* **CrewKickoffStartedEvent**：当 Crew 开始执行时发出
* **CrewKickoffCompletedEvent**：当 Crew 完成执行时发出
* **CrewKickoffFailedEvent**：当 Crew 未能完成执行时发出
* **CrewTestStartedEvent**：当 Crew 开始测试时发出
* **CrewTestCompletedEvent**：当 Crew 完成测试时发出
* **CrewTestFailedEvent**：当 Crew 未能完成测试时发出
* **CrewTrainStartedEvent**：当 Crew 开始训练时发出
* **CrewTrainCompletedEvent**：当 Crew 完成训练时发出
* **CrewTrainFailedEvent**：当 Crew 未能完成训练时发出

### Agent 事件

* **AgentExecutionStartedEvent**：当 Agent 开始执行任务时发出
* **AgentExecutionCompletedEvent**：当 Agent 完成执行任务时发出
* **AgentExecutionErrorEvent**：当 Agent 在执行过程中遇到错误时发出

### 任务事件

* **TaskStartedEvent**：当任务开始执行时发出
* **TaskCompletedEvent**：当任务完成执行时发出
* **TaskFailedEvent**：当任务未能完成执行时发出
* **TaskEvaluationEvent**：当任务被评估时发出

### 工具使用事件

* **ToolUsageStartedEvent**：当工具执行开始时发出
* **ToolUsageFinishedEvent**：当工具执行完成时发出
* **ToolUsageErrorEvent**：当工具执行遇到错误时发出
* **ToolValidateInputErrorEvent**：当工具输入验证遇到错误时发出
* **ToolExecutionErrorEvent**：当工具执行遇到错误时发出
* **ToolSelectionErrorEvent**：当选择工具时出现错误时发出

### 知识事件

* **KnowledgeRetrievalStartedEvent**：当知识检索开始时发出
* **KnowledgeRetrievalCompletedEvent**：当知识检索完成时发出
* **KnowledgeQueryStartedEvent**：当知识查询开始时发出
* **KnowledgeQueryCompletedEvent**：当知识查询完成时发出
* **KnowledgeQueryFailedEvent**：当知识查询失败时发出
* **KnowledgeSearchQueryFailedEvent**：当知识搜索查询失败时发出

### LLM 护栏事件

* **LLMGuardrailStartedEvent**：当护栏验证开始时发出。包含有关应用的护栏和重试计数的详细信息。
* **LLMGuardrailCompletedEvent**：当护栏验证完成时发出。包含验证成功/失败的详细信息、结果和任何错误消息。

### Flow 事件

* **FlowCreatedEvent**：当 Flow 被创建时发出
* **FlowStartedEvent**：当 Flow 开始执行时发出
* **FlowFinishedEvent**：当 Flow 完成执行时发出
* **FlowPlotEvent**：当 Flow 被绘制时发出
* **MethodExecutionStartedEvent**：当 Flow 方法开始执行时发出
* **MethodExecutionFinishedEvent**：当 Flow 方法完成执行时发出
* **MethodExecutionFailedEvent**：当 Flow 方法未能完成执行时发出

### LLM 事件

* **LLMCallStartedEvent**：当 LLM 调用开始时发出
* **LLMCallCompletedEvent**：当 LLM 调用完成时发出
* **LLMCallFailedEvent**：当 LLM 调用失败时发出
* **LLMStreamChunkEvent**：在流式传输 LLM 响应期间为每个接收到的数据块发出

### 内存事件

* **MemoryQueryStartedEvent**：当内存查询开始时发出。包含查询、限制和可选的分数阈值。
* **MemoryQueryCompletedEvent**：当内存查询成功完成时发出。包含查询、结果、限制、分数阈值和查询执行时间。
* **MemoryQueryFailedEvent**：当内存查询失败时发出。包含查询、限制、分数阈值和错误消息。
* **MemorySaveStartedEvent**：当内存保存操作开始时发出。包含要保存的值、元数据和可选的代理角色。
* **MemorySaveCompletedEvent**：当内存保存操作成功完成时发出。包含保存的值、元数据、代理角色和保存执行时间。
* **MemorySaveFailedEvent**：当内存保存操作失败时发出。包含值、元数据、代理角色和错误消息。
* **MemoryRetrievalStartedEvent**：当任务提示的内存检索开始时发出。包含可选的任务 ID。
* **MemoryRetrievalCompletedEvent**：当任务提示的内存检索成功完成时发出。包含任务 ID、内存内容和检索执行时间。

## 事件处理程序结构

每个事件处理程序接收两个参数：

1. **source**：发出事件的对象
2. **event**：事件实例，包含特定于事件的数据

事件对象的结构取决于事件类型，但所有事件都继承自 `BaseEvent` 并包括：

* **timestamp**：事件发出时的时间
* **type**：事件类型的字符串标识符

其他字段因事件类型而异。例如，`CrewKickoffCompletedEvent` 包括 `crew_name` 和 `output` 字段。

## 高级用法：作用域处理程序

对于临时事件处理（对测试或特定操作有用），您可以使用 `scoped_handlers` 上下文管理器：

```python  theme={null}
from crewai.events import crewai_event_bus, CrewKickoffStartedEvent

with crewai_event_bus.scoped_handlers():
    @crewai_event_bus.on(CrewKickoffStartedEvent)
    def temp_handler(source, event):
        print("This handler only exists within this context")

    # 执行会发出事件的操作

# 在上下文之外，临时处理程序被移除
```

## 用例

事件监听器可用于多种目的：

1. **日志记录和监控**：跟踪您的 Crew 的执行并记录重要事件
2. **分析**：收集有关您的 Crew 性能和行为的数据
3. **调试**：设置临时监听器以调试特定问题
4. **集成**：将 CrewAI 与外部系统（如监控平台、数据库或通知服务）连接
5. **自定义行为**：基于特定事件触发自定义操作

## 最佳实践

1. **保持处理程序轻量**：事件处理程序应该是轻量级的，避免阻塞操作
2. **错误处理**：在事件处理程序中包含适当的错误处理，以防止异常影响主执行
3. **清理**：如果您的监听器分配资源，确保它们被正确清理
4. **选择性监听**：只监听您实际需要处理的事件
5. **测试**：单独测试您的事件监听器，确保它们按预期运行

通过利用 CrewAI 的事件系统，您可以扩展其功能并将其与您现有的基础设施无缝集成。