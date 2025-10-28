# 指纹识别

> 了解如何使用 CrewAI 的指纹系统来在整个生命周期中唯一识别和跟踪组件。

## 概述

CrewAI 中的指纹提供了一种在整个生命周期中唯一识别和跟踪组件的方法。每个 `Agent`（代理）、`Crew`（团队）和 `Task`（任务）在创建时会自动接收一个唯一的指纹，该指纹不能手动覆盖。

这些指纹可用于：

* 审计和跟踪组件使用情况
* 确保组件身份完整性
* 向组件附加元数据
* 创建可追溯的操作链

## 指纹如何工作

指纹是 `crewai.security` 模块中 `Fingerprint` 类的一个实例。每个指纹包含：

* UUID 字符串：组件的唯一标识符，自动生成且不能手动设置
* 创建时间戳：生成指纹的时间，自动设置且不能手动修改
* 元数据：可自定义的附加信息字典

组件在创建时自动生成并分配指纹。每个组件通过只读属性公开其指纹。

## 基本用法

### 访问指纹

```python  theme={null}
from crewai import Agent, Crew, Task

# 创建组件 - 指纹会自动生成
agent = Agent(
    role="Data Scientist",
    goal="Analyze data",
    backstory="Expert in data analysis"
)

crew = Crew(
    agents=[agent],
    tasks=[]
)

task = Task(
    description="Analyze customer data",
    expected_output="Insights from data analysis",
    agent=agent
)

# 访问指纹
agent_fingerprint = agent.fingerprint
crew_fingerprint = crew.fingerprint
task_fingerprint = task.fingerprint

# 打印 UUID 字符串
print(f"Agent fingerprint: {agent_fingerprint.uuid_str}")
print(f"Crew fingerprint: {crew_fingerprint.uuid_str}")
print(f"Task fingerprint: {task_fingerprint.uuid_str}")
```

### 使用指纹元数据

您可以为指纹添加元数据以提供额外的上下文：

```python  theme={null}
# 向代理的指纹添加元数据
agent.security_config.fingerprint.metadata = {
    "version": "1.0",
    "department": "Data Science",
    "project": "Customer Analysis"
}

# 访问元数据
print(f"Agent metadata: {agent.fingerprint.metadata}")
```

## 指纹持久性

指纹设计为在组件的整个生命周期中保持不变。如果您修改组件，指纹保持不变：

```python  theme={null}
original_fingerprint = agent.fingerprint.uuid_str

# 修改代理
agent.goal = "New goal for analysis"

# 指纹保持不变
assert agent.fingerprint.uuid_str == original_fingerprint
```

## 确定性指纹

虽然您不能直接设置 UUID 和创建时间戳，但可以使用带有种子的 `generate` 方法创建确定性指纹：

```python  theme={null}
from crewai.security import Fingerprint

# 使用种子字符串创建确定性指纹
deterministic_fingerprint = Fingerprint.generate(seed="my-agent-id")

# 相同的种子总是产生相同的指纹
same_fingerprint = Fingerprint.generate(seed="my-agent-id")
assert deterministic_fingerprint.uuid_str == same_fingerprint.uuid_str

# 您也可以设置元数据
custom_fingerprint = Fingerprint.generate(
    seed="my-agent-id",
    metadata={"version": "1.0"}
)
```

## 高级用法

### 指纹结构

每个指纹具有以下结构：

```python  theme={null}
from crewai.security import Fingerprint

fingerprint = agent.fingerprint

# UUID 字符串 - 唯一标识符（自动生成）
uuid_str = fingerprint.uuid_str  # 例如，"123e4567-e89b-12d3-a456-426614174000"

# 创建时间戳（自动生成）
created_at = fingerprint.created_at  # 一个 datetime 对象

# 元数据 - 用于附加信息（可自定义）
metadata = fingerprint.metadata  # 一个字典，默认为 {}
```