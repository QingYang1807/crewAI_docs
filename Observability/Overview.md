# 概述

> 通过全面的观测工具来监控、评估和优化您的 CrewAI 智能体

## CrewAI 的可观测性

可观测性对于理解您的 CrewAI 智能体如何表现、识别瓶颈以及确保在生产环境中可靠运行至关重要。本节介绍了各种为您的智能体工作流提供监控、评估和优化功能的工具和平台。

## 为什么可观测性很重要

* **性能监控**：跟踪智能体执行时间、令牌使用情况和资源消耗
* **质量保证**：评估不同场景下的输出质量和一致性
* **调试**：识别并解决智能体行为和任务执行中的问题
* **成本管理**：监控 LLM API 使用情况和相关成本
* **持续改进**：收集洞见以随时间优化智能体性能

## 可用的可观测性工具

### 监控与追踪平台

<CardGroup cols={2}>
  <Card title="LangDB" icon="database" href="/en/observability/langdb">
    为 CrewAI 工作流提供端到端追踪，自动捕获智能体交互。
  </Card>

  <Card title="OpenLIT" icon="magnifying-glass-chart" href="/en/observability/openlit">
    基于 OpenTelemetry 的原生监控，具有成本跟踪和性能分析功能。
  </Card>

  <Card title="MLflow" icon="bars-staggered" href="/en/observability/mlflow">
    具有追踪和评估功能的机器学习生命周期管理平台。
  </Card>

  <Card title="Langfuse" icon="link" href="/en/observability/langfuse">
    具有详细追踪和分析功能的 LLM 工程平台。
  </Card>

  <Card title="Langtrace" icon="chart-line" href="/en/observability/langtrace">
    面向 LLM 和智能体框架的开源可观测性解决方案。
  </Card>

  <Card title="Arize Phoenix" icon="meteor" href="/en/observability/arize-phoenix">
    用于监控和故障排除的 AI 可观测性平台。
  </Card>

  <Card title="Portkey" icon="key" href="/en/observability/portkey">
    具有全面监控和可靠性功能的 AI 网关。
  </Card>

  <Card title="Opik" icon="meteor" href="/en/observability/opik">
    通过全面追踪功能调试、评估和监控 LLM 应用程序。
  </Card>

  <Card title="Weave" icon="network-wired" href="/en/observability/weave">
    用于跟踪和评估 AI 应用程序的 Weights & Biases 平台。
  </Card>
</CardGroup>

### 评估与质量保证

<CardGroup cols={2}>
  <Card title="Patronus AI" icon="shield-check" href="/en/observability/patronus-evaluation">
    用于 LLM 输出和智能体行为的综合评估平台。
  </Card>
</CardGroup>

## 关键可观测性指标

### 性能指标

* **执行时间**：智能体完成任务所需的时间
* **令牌使用量**：LLM 调用消耗的输入/输出令牌
* **API 延迟**：外部服务的响应时间
* **成功率**：成功完成的任务百分比

### 质量指标

* **输出准确性**：智能体响应的正确性
* **一致性**：在相似输入下的可靠性
* **相关性**：输出与预期结果的匹配程度
* **安全性**：对内容政策和指南的合规性

### 成本指标

* **API 成本**：LLM 提供商使用产生的费用
* **资源利用率**：计算和内存消耗
* **每任务成本**：智能体操作的经济效率
* **预算跟踪**：对支出限制的监控

## 入门指南

1. **选择工具**：选择符合您需求的可观测性平台
2. **代码配置**：在您的 CrewAI 应用程序中添加监控功能
3. **设置仪表板**：为关键指标配置可视化
4. **定义警报**：为重要事件创建通知
5. **建立基准**：测量初始性能以供比较
6. **迭代和改进**：利用洞见优化您的智能体

## 最佳实践

### 开发阶段

* 使用详细追踪来了解智能体行为
* 在开发早期实施评估指标
* 在测试期间监控资源使用情况
* 设置自动化质量检查

### 生产阶段

* 实施全面监控和警报
* 随时间跟踪性能趋势
* 监控异常和性能下降
* 保持成本可见性和控制

### 持续改进

* 定期性能审查和优化
* 不同智能体配置的 A/B 测试
* 质量改进的反馈循环
* 记录经验教训

选择最适合您用例、基础设施和监控需求的可观测性工具，确保您的 CrewAI 智能体能够可靠高效地运行。