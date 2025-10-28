# 概览

> 通过全面的指南和教程，学习如何构建、自定义和优化您的 CrewAI 应用程序

## 学习 CrewAI

本部分提供全面的指南和教程，帮助您从基本概念到高级技巧全面掌握 CrewAI。无论您是刚刚入门还是希望优化现有实现，这些资源都将指导您了解构建强大 AI 代理工作流程的各个方面。

## 入门指南

### 核心概念

<CardGroup cols={2}>
  <Card title="顺序处理" icon="list-ol" href="/en/learn/sequential-process">
    学习如何按顺序执行任务，以构建结构化的工作流程。
  </Card>

  <Card title="层级处理" icon="sitemap" href="/en/learn/hierarchical-process">
    实现由管理代理监督工作流程的层级任务执行。
  </Card>

  <Card title="条件任务" icon="code-branch" href="/en/learn/conditional-tasks">
    基于结果创建具有条件任务执行的动态工作流程。
  </Card>

  <Card title="异步启动" icon="bolt" href="/en/learn/kickoff-async">
    异步执行团队以提高性能和并发性。
  </Card>
</CardGroup>

### 代理开发

<CardGroup cols={2}>
  <Card title="自定义代理" icon="user-gear" href="/en/learn/customizing-agents">
    学习如何自定义代理的行为、角色和能力。
  </Card>

  <Card title="编码代理" icon="code" href="/en/learn/coding-agents">
    构建能够自动编写、执行和调试代码的代理。
  </Card>

  <Card title="多模态代理" icon="images" href="/en/learn/multimodal-agents">
    创建能够处理文本、图像和其他媒体类型的代理。
  </Card>

  <Card title="自定义管理代理" icon="user-tie" href="/en/learn/custom-manager-agent">
    为复杂的层级工作流程实现自定义管理代理。
  </Card>
</CardGroup>

## 高级功能

### 工作流控制

<CardGroup cols={2}>
  <Card title="人在循环" icon="user-check" href="/en/learn/human-in-the-loop">
    将人工监督和干预整合到代理工作流程中。
  </Card>

  <Card title="执行时人工输入" icon="hand-paper" href="/en/learn/human-input-on-execution">
    在任务执行期间允许人工输入以进行动态决策。
  </Card>

  <Card title="重播任务" icon="rotate-left" href="/en/learn/replay-tasks-from-latest-crew-kickoff">
    从之前的团队执行中重播和恢复任务。
  </Card>

  <Card title="逐个启动" icon="repeat" href="/en/learn/kickoff-for-each">
    高效地使用不同输入多次执行团队。
  </Card>
</CardGroup>

### 自定义与集成

<CardGroup cols={2}>
  <Card title="自定义 LLM" icon="brain" href="/en/learn/custom-llm">
    将自定义语言模型和提供商与 CrewAI 集成。
  </Card>

  <Card title="LLM 连接" icon="link" href="/en/learn/llm-connections">
    配置和管理与各种 LLM 提供商的连接。
  </Card>

  <Card title="创建自定义工具" icon="wrench" href="/en/learn/create-custom-tools">
    构建自定义工具以扩展代理能力。
  </Card>

  <Card title="使用注解" icon="at" href="/en/learn/using-annotations">
    使用 Python 注解来编写更清晰、更易维护的代码。
  </Card>
</CardGroup>

## 专业应用

### 内容与媒体

<CardGroup cols={2}>
  <Card title="DALL-E 图像生成" icon="image" href="/en/learn/dalle-image-generation">
    使用与您的代理集成的 DALL-E 生成图像。
  </Card>

  <Card title="自带代理" icon="user-plus" href="/en/learn/bring-your-own-agent">
    将现有代理和模型集成到 CrewAI 工作流程中。
  </Card>
</CardGroup>

### 工具管理

<CardGroup cols={2}>
  <Card title="强制工具输出作为结果" icon="hammer" href="/en/learn/force-tool-output-as-result">
    配置工具将其输出直接作为任务结果返回。
  </Card>
</CardGroup>

## 学习路径推荐

### 初学者

1. 从**顺序处理**开始，了解基本工作流程执行
2. 学习**自定义代理**以创建有效的代理配置
3. 探索**创建自定义工具**以扩展功能
4. 尝试**人在循环**以实现交互式工作流程

### 中级用户

1. 掌握**层级处理**以应对复杂的多代理系统
2. 实现**条件任务**以创建动态工作流程
3. 使用**异步启动**进行性能优化
4. 集成**自定义 LLM**以使用专业模型

### 高级用户

1. 构建**多模态代理**以处理复杂的媒体处理
2. 创建**自定义管理代理**以实现复杂的编排
3. 实现**自带代理**以构建混合系统
4. 使用**重播任务**以实现强大的错误恢复

## 最佳实践

### 开发

* **从简单开始**：在增加复杂性之前，先从基本的顺序工作流程开始
* **增量测试**：在集成到更大系统之前，先测试每个组件
* **使用注解**：利用 Python 注解编写更清晰、更易维护的代码
* **自定义工具**：构建可在不同代理之间共享的可重用工具

### 生产

* **错误处理**：实现强大的错误处理和恢复机制
* **性能**：使用异步执行并优化 LLM 调用以获得更好的性能
* **监控**：集成可观察性工具以跟踪代理性能
* **人工监督**：为关键决策加入人工检查点

### 优化

* **资源管理**：监控和优化令牌使用情况和 API 成本
* **工作流程设计**：设计最小化不必要 LLM 调用的工作流程
* **工具效率**：创建高效的工具，以最小的开销提供最大价值
* **迭代改进**：使用反馈和指标持续改进代理性能

## 获取帮助

* **文档**：每个指南都包含详细的示例和说明
* **社区**：加入 [CrewAI 论坛](https://community.crewai.com) 进行讨论和获取支持
* **示例**：查看示例部分以获取完整的工作实现
* **支持**：联系 [support@crewai.com](mailto:support@crewai.com) 获取技术帮助

从符合您当前需求的指南开始，并在您熟悉基础知识后逐步探索更高级的主题。