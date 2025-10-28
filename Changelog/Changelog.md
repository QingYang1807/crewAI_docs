# 更新日志

> CrewAI 的产品更新、改进和错误修复

<Update label="2025年9月30日">
  ## v1.0.0a1

  [在 GitHub 上查看发布](https://github.com/crewAIInc/crewAI/releases/tag/1.0.0a1)

  ## 更新内容

  ### 核心改进与修复

  * 修复了代理配置中 `actions` 的权限处理
  * 更新了 CI 工作流程和发布发布流程以支持新的单体仓库结构
  * 将 Python 支持提升至 3.13 并刷新了工作空间元数据

  ### 新功能与增强

  * 为 `Agent` 添加了 `apps` 和 `actions` 属性，以实现更丰富的运行时控制
  * 将 `crewai-tools` 仓库合并到主工作空间（单体仓库）
  * 将所有包升级到 1.0.0a1 以标记 alpha 里程碑

  ### 清理与基础设施

  * 提供了带有版本固定和发布策略的新 CI 流水线
  * 统一了内部代码以连贯地管理多个包
</Update>

<Update label="2025年9月26日">
  ## v0.201.1

  [在 GitHub 上查看发布](https://github.com/crewAIInc/crewAI/releases/tag/0.201.1)

  ## 更新内容

  ### 核心改进与修复

  * 将 Watson 嵌入提供程序重命名为 `watsonx` 并刷新了环境变量前缀
  * 为 `watsonx` 和 `voyageai` 嵌入提供程序添加了 ChromaDB 兼容性

  ### 清理与弃用

  * 标准化了所有嵌入提供程序的环境变量前缀
  * 将 CrewAI 升级到 0.201.1 并更新了内部依赖项
</Update>

<Update label="2025年9月24日">
  ## v0.201.0

  [在 GitHub 上查看发布](https://github.com/crewAIInc/crewAI/releases/tag/0.201.0)

  ## 更新内容

  ### 核心改进与修复

  * 在 `_create_reasoning_plan` 中使 `ready` 参数变为可选
  * 修复了嵌入器配置的嵌套配置处理
  * 添加了 `batch_size` 支持以避免令牌限制错误
  * 更正了快速入门文档目录命名
  * 解决了测试持续时间缓存问题和事件导出
  * 为团队设置添加了回退逻辑

  ### 新功能与增强

  * 引入了线程安全的平台上下文管理
  * 添加了 `crewai uv` 包装器命令以从 CLI 运行 `uv`
  * 支持将跟踪标记为失败以进行可观察性工作流程
  * 添加了自定义嵌入类型和提供程序迁移支持
  * 将 ChromaDB 升级到 v1.1.0，附带兼容性修复和类型改进
  * 添加了 Pydantic 兼容的导入验证并重组了依赖组

  ### 文档与指南

  * 为最近的发布（0.193.x 系列）更新了变更日志覆盖范围
  * 为 LLM Guardrail 事件记录了元数据支持
  * 为回退行为和配置可见性添加了指导

  ### 清理与弃用

  * 解决了跨模块的 Ruff 和 MyPy 问题
  * 改进了类型注释并整合了实用工具
  * 弃用了传统实用工具，改用 Pydantic 兼容的导入

  ### 贡献者

  * @qizwiz（首次贡献）
</Update>

<Update label="2025年9月20日">
  ## v0.193.2

  [在 GitHub 上查看发布](https://github.com/crewAIInc/crewAI/releases/tag/0.193.2)

  ## 更新内容

  * 更新了 pyproject 模板以使用正确的版本
</Update>

<Update label="2025年9月20日">
  ## v0.193.1

  [在 GitHub 上查看发布](https://github.com/crewAIInc/crewAI/releases/tag/0.193.1)

  ## 更新内容

  * 一系列小修复和 linter 改进
</Update>

<Update label="2025年9月19日">
  ## v0.193.0

  ## 核心改进与修复

  * 修复了 OpenAI 适配器初始化期间 `model` 参数的处理
  * 解决了 CI 工作流程中的测试持续时间缓存问题
  * 修复了与代理重复使用工具相关的不可靠测试
  * 为了一致的模块行为，向 `__init__.py` 添加了缺失的事件导出
  * 从 Mem0 的元数据中删除了消息存储以减少膨胀
  * 为向后兼容性修复了向量搜索中的 L2 距离度量支持

  ## 新功能与增强

  * 引入了线程安全的平台上下文管理
  * 为优化的 `pytest-split` 运行添加了测试持续时间缓存
  * 改进了临时跟踪以实现更好的跟踪控制
  * 使 RAG、知识和记忆的搜索参数完全可配置
  * 使 ChromaDB 能够使用 OpenAI API 进行嵌入功能
  * 为用户级洞察添加了更深入的可观察性工具
  * 统一了具有特定实例客户端支持的 RAG 存储系统

  ## 文档与指南

  * 更新了 `RagTool` 引用以反映 CrewAI 原生 RAG 实现
  * 使用类型注释和文档字符串改进了 `langgraph` 和 `openai` 代理适配器的内部文档
</Update>

<Update label="2025年9月11日">
  ## v0.186.1

  [在 GitHub 上查看发布](https://github.com/crewAIInc/crewAI/releases/tag/0.186.1)

  ## 更新内容

  * 修复了找不到版本并静默失败恢复的问题
  * 将 CrewAI 版本升级到 0.186.1 并更新了 CLI 中的依赖项
</Update>

<Update label="2025年9月10日">
  ## v0.186.0

  [在 GitHub 上查看发布](https://github.com/crewAIInc/crewAI/releases/tag/0.186.0)

  ## 更新内容

  * 有关详细更改，请参阅 GitHub 发布说明
</Update>

<Update label="2025年9月4日">
  ## v0.177.0

  ## 核心改进与修复

  * 实现了 `rag` 包与当前实现之间的对等
  * 通过任务和代理元数据增强了 LLM 事件处理
  * 通过用 `None` 替换来修复了可变默认参数
  * 在初始化期间抑制了 Pydantic 弃用警告
  * 修复了 `README.md` 中损坏的示例链接
  * 为兼容性删除了 Python 3.12+ 仅限的 Ruff 规则
  * 将 CI 工作流程迁移到使用 `uv` 并更新了开发工具

  ## 新功能与增强

  * 添加了跟踪改进和清理
  * 通过将 `events` 模块移动到 `crewai.events` 来集中事件逻辑

  ## 文档与指南

  * 更新了企业操作身份验证令牌部分文档
  * 发布了 `v0.175.0` 发布的文档更新

  ## 清理与重构

  * 将解析器重构为模块化函数以获得更好的结构
</Update>

<Update label="2025年8月28日">
  ## v0.175.0

  ## 核心改进与修复

  * 修复了 `crewai update` 期间 `tool` 部分的迁移
  * 恢复了 OpenAI 固定版本：现在需要 `openai >=1.13.3`，因为修复了导入问题
  * 修复了不可靠的测试并改进了测试稳定性
  * 改进了 HITL 和循环流程的 `Flow` 侦听器可恢复性
  * 增强了 `PlusAPI` 和 `TraceBatchManager` 中的超时处理
  * 批量处理实体内存项以减少冗余操作

  ## 新功能与增强

  * 为 `Flow.start()` 方法添加了对额外参数的支持
  * 在详细 CLI 输出中显示任务名称
  * 添加了集中化的嵌入类型并引入了基础嵌入客户端
  * 为 ChromaDB 和 Qdrant 引入了通用客户端
  * 添加了 `crewai config reset` 支持以清除令牌
  * 启用了 `crewai_trigger_payload` 自动注入
  * 简化了 RAG 客户端初始化并引入了 RAG 配置系统
  * 添加了 Qdrant RAG 提供程序支持
  * 通过更好事件数据改进了跟踪
  * 添加了在 `crewai login` 上删除 Auth0 和电子邮件条目的支持

  ## 文档与指南

  * 添加了自动化触发器文档
  * 修复了 API 参考 OpenAPI 源和重定向
  * 向文档添加了混合搜索 alpha 参数

  ## 清理与弃用

  * 为 `Task.max_retries` 添加了弃用通知
  * 从登录流程中删除了 Auth0 依赖
</Update>

<Update label="2025年8月19日">
  ## v0.165.1

  ## 核心改进与修复

  * 通过为 `configparser` 将配置值转换为字符串，修复了 `XMLSearchTool` 中的兼容性
  * 修复了涉及 `PytestUnraisableExceptionWarning` 的不可靠 Pytest 测试
  * 在测试套件中模拟了遥测以实现更稳定的 CI 运行
  * 将 Chroma 锁文件处理移至 `db_storage_path`
  * 忽略了 `chromadb` 的弃用警告
  * 由于 `ResponseTextConfigParam` 导入问题，将 OpenAI 版本固定为 `<1.100.0`

  ## 新功能与增强

  * 将交换的代理消息包含到 `ExternalMemory` 元数据中
  * 自动注入了 `crewai_trigger_payload`
  * 将内部标志 `inject_trigger_input` 重命名为 `allow_crewai_trigger_context`
  * 继续跟踪改进和临时跟踪逻辑
  * 整合了跟踪逻辑条件
  * 为 `Mem0` 中的 `agent_id` 链接内存条目添加了支持

  ## 文档与指南

  * 为工具存储库文档添加了示例
  * 为短期记忆和实体记忆集成更新了 Mem0 文档
  * 修订了韩语翻译并改进了句子结构

  ## 清理与杂务

  * 删除了已弃用的 AgentOps 集成
</Update>

<Update label="2025年8月19日">
  ## v0.165.0

  ## 核心改进与修复

  * 通过为 `configparser` 将配置值转换为字符串，修复了 `XMLSearchTool` 中的兼容性
  * 修复了涉及 `PytestUnraisableExceptionWarning` 的不可靠 Pytest 测试
  * 在测试套件中模拟了遥测以实现更稳定的 CI 运行
  * 将 Chroma 锁文件处理移至 `db_storage_path`
  * 忽略了 `chromadb` 的弃用警告
  * 由于 `ResponseTextConfigParam` 导入问题，将 OpenAI 版本固定为 `<1.100.0`

  ## 新功能与增强

  * 将交换的代理消息包含到 `ExternalMemory` 元数据中
  * 自动注入了 `crewai_trigger_payload`
  * 将内部标志 `inject_trigger_input` 重命名为 `allow_crewai_trigger_context`
  * 继续跟踪改进和临时跟踪逻辑
  * 整合了跟踪逻辑条件
  * 为 `Mem0` 中的 `agent_id` 链接内存条目添加了支持

  ## 文档与指南

  * 为工具存储库文档添加了示例
  * 为短期记忆和实体记忆集成更新了 Mem0 文档
  * 修订了韩语翻译并改进了句子结构

  ## 清理与杂务

  * 删除了已弃用的 AgentOps 集成
</Update>

<Update label="2025年8月13日">
  ## v0.159.0

  ## 核心改进与修复

  * 改进了 LLM 消息格式性能以获得更好的运行时效率
  * 修复了在企业配置身份验证/参数中使用错误端点的问题
  * 为了在部分流程恢复期间的稳定性，注释掉了侦听器可恢复性检查

  ## 新功能与增强

  * 向 CLI 添加了 `enterprise configure` 命令以简化企业设置
  * 引入了部分流程可恢复性支持

  ## 文档与指南

  * 为新工具添加了文档
  * 添加了韩语翻译
  * 使用 TrueFoundry 集成详细信息更新了文档
  * 添加了 RBAC 文档和常规清理
  * 修复了 API 参考并改进了 EN、PT-BR 和 KO 的示例/手册
</Update>

<Update label="2025年8月6日">
  ## v0.157.0

  ## v0.157.0 更新内容

  ## 核心改进与修复

  * 为长输入工具启用了自动换行
  * 允许使用 `BaseModel` 条目保持 Flow 状态
  * 使用 `partition()` 优化了字符串操作以提高性能
  * 放弃了对已弃用的用户记忆系统的支持
  * 将 LiteLLM 版本提升到 `1.74.9`
  * 修复了 CLI 以在导入期间更清楚地显示缺失的模块
  * 支持使用 Okta 进行设备授权

  ## 新功能与增强

  * 添加了带有测试的 `crewai config` CLI 命令组
  * 为 `crew.name` 添加了默认值支持
  * 引入了初始跟踪功能
  * 添加了 LangDB 集成支持
  * 添加了 CLI 配置文档支持

  ## 文档与指南

  * 使用 `connect_timeout` 属性更新了 MCP 文档
  * 添加了 LangDB 集成文档
  * 添加了 CLI 配置文档
  * 常规功能文档更新和清理
</Update>

<Update label="2025年7月30日">
  ## v0.152.0

  ## 核心改进与修复

  * 删除了 `crewai signup` 引用，并将其替换为 `crewai login`
  * 修复了使用 `agent_id` 向 Mem0 添加记忆的支持
  * 更改了 Mem0 配置中的默认值
  * 更新了导入错误以清楚地显示缺失的模块文件
  * 为事件时间戳添加了时区支持

  ## 新功能与增强

  * 增强了 `Flow` 类以支持自定义流程名称
  * 将 RAG 组件重构为专用顶级模块

  ## 文档与指南

  * 修复了 Google Vertex AI 文档中的错误模型命名
</Update>

<Update label="2025年7月23日">
  ## v0.150.0

  ## 核心改进与修复

  * 在 Chroma 客户端初始化周围使用了文件锁
  * 删除了与没有 FTS5 的 SQLite 相关的变通方法
  * 自动删除了 LLM 模型不支持的 `stop` 参数
  * 修复了 `save` 方法并更新了相关测试用例
  * 当最后一条消息来自助手时，修复了 Ollama 模型的消息处理
  * 删除了 LLM 调用错误时的重复打印
  * 向 `UserMemory` 添加了弃用通知
  * 将 LiteLLM 升级到版本 1.74.3

  ## 新功能与增强

  * 添加了通过内部 LLM 类进行临时工具调用的支持
  * 将 Mem0 存储从 v1.1 更新到 v2

  ## 文档与指南

  * 修复了 neatlogs 文档
  * 将 Tavily Search & Extractor 工具添加到 Search-Research 套件
  * 为 `SerperScrapeWebsiteTool` 添加了文档并重新组织了 Serper 部分
  * 常规文档更新和改进

  ## crewai-tools v0.58.0

  ### 新工具/增强

  * **SerperScrapeWebsiteTool**：添加了用于从 URL 提取干净内容的工具
  * **Bedrock AgentCore**：为 Bedrock 代理集成了浏览器和代码解释器工具包
  * **Stagehand 更新**：重构并更新了 Stagehand 集成

  ### 修复与清理

  * **FTS5 支持**：在测试工作流程中启用了 SQLite FTS5 以改进文本搜索
  * **测试加速**：并行化了 GitHub Actions 测试套件以加快 CI 运行
  * **清理**：由于 FTS5 支持可用，删除了 SQLite 变通方法
    **MongoDBVectorSearchTool**：修复了序列化和模式处理
</Update>

<Update label="2025年7月16日">
  ## v0.148.0

  ## 核心改进与修复

  * 使用了生产 WorkOS 环境 ID
  * 向测试工作流程添加了 SQLite FTS5 支持
  * 修复了代理知识处理
  * 改为使用 `BaseLLM` 类而不是 `LLM` 进行比较
  * 修复了 `Task` 类中缺失的 `create_directory` 参数

  ## 新功能与增强

  * 引入了代理评估功能
  * 添加了评估器实验和回归测试方法
  * 实现了线程安全的 `AgentEvaluator`
  * 启用了代理评估的事件发射
  * 支持评估单个 `Agent` 和 `LiteAgent`
  * 添加了与 `neatlogs` 的集成
  * 为 LLM guardrail 事件添加了团队上下文跟踪

  ## 文档与指南

  * 为 `guardrail` 属性和用法示例添加了文档
  * 添加了 `neatlogs` 的集成指南
  * 更新了代理存储库和 `Agent.kickoff` 用法的文档
</Update>

<Update label="2025年7月9日">
  ## v0.141.0

  ## 核心改进与修复

  * 通过并行化加快了 GitHub Actions 测试

  ## 新功能与增强

  * 为 LLM guardrail 事件添加了团队上下文跟踪

  ## 文档与指南

  * 添加了代理存储库用法的文档
  * 添加了 `Agent.kickoff` 方法的文档
</Update>

<Update label="2025年7月2日">
  ## v0.140.0

  ## 核心改进与修复

  * 修复了测试提示中的拼写错误
  * 通过在团队创建期间剥离尾部斜杠来修复项目名称标准化
  * 确保环境变量以大写形式写入
  * 更新了 LiteLLM 依赖项
  * 重构了 `RAGStorage` 中的集合处理
  * 实现了 PEP 621 动态版本控制

  ## 新功能与增强

  * 添加了按任务和代理跟踪 LLM 调用的能力
  * 引入了 `MemoryEvents` 以监控内存使用情况
  * 为内存系统和 LLM guardrail 事件添加了控制台日志记录
  * 改进了对高达 7B 参数模型的数据训练支持
  * 添加了 Scarf 和 Reo.dev 分析跟踪
  * CLI workos 登录

  ## 文档与指南

  * 更新了 CLI LLM 文档
  * 在文档中添加了 Nebius 集成
  * 更正了安装和 pt-BR 文档中的拼写错误
  * 添加了关于 `MemoryEvents` 的文档
  * 实现了文档重定向并包含了开发工具
</Update>

<Update label="2025年6月25日">
  ## v0.134.0

  ## 核心改进与修复

  * 修复了工具参数语法
  * 修复了 `Task` 中的类型注释
  * 修复了从 GitHub 检索 LLM 数据时的 SSL 错误
  * 确保与 Pydantic 2.7.x 兼容
  * 从项目依赖项中删除了 `mkdocs`
  * 将 Langfuse 代码示例升级到使用 Python SDK v3
  * 在 `mem0` 存储中添加了清理角色功能
  * 改进了记忆重置期间的团队搜索
  * 改进了控制台打印机输出

  ## 新功能与增强

  * 添加了从定义的 `Tool` 属性初始化工具的支持
  * 添加了在 `CrewBase` 中使用 MCP 工具的官方方式
  * 增强了 MCP 工具支持，允许在 `CrewBase` 中为每个代理选择多个工具
  * 添加了 Oxylabs 网络抓取工具

  ## 文档与指南

  * 更新了 `quickstart.mdx`
  * 添加了关于 `LLMGuardrail` 事件的文档
  * 使用全面的服务集成详细信息更新了文档
  * 更新了 MCP 和企业工具的推荐过滤器
  * 更新了代理可观察性的 Maxim 文档
  * 添加了 pt-BR 文档翻译
  * 常规文档改进
</Update>

<Update label="2025年6月12日">
  ## v0.130.0

  ## 核心改进与修复

  * 删除了与工具结果输出相关的重复消息
  * 修复了启动时 `usage_metrics` 中缺失的 `manager_agent` 令牌
  * 修复了遥测单例以尊重动态环境变量
  * 修复了流程状态日志可能隐藏人工输入的问题
  * 增加了流程绘图的默认 X 轴间距

  ## 新功能与增强

  * 在 CLI 中添加了对多组织操作的支持
  * 启用了异步工具执行以实现更高效的工作流程
  * 引入了具有 Guardrail 集成的 `LiteAgent`
  * 将 `LiteLLM` 升级到支持最新的 OpenAI 版本

  ## 文档与指南

  * 为工具存储库记录了最低 `UV` 版本
  * 改进了幻觉 Guardrail 的示例
  * 为 LLM 用法更新了规划文档
  * 在代理可观察性中添加了 Maxim 支持文档
  * 通过企业功能图像扩展了集成文档
  * 修复了持久性指南
  * 更新了 Python 版本支持以支持 python 3.13.x
</Update>

<Update label="2025年6月5日">
  ## v0.126.0

  ### 更新内容

  #### 核心改进与修复

  * 添加了对 Python 3.13 的支持
  * 修复了代理知识源问题
  * 保留了来自工具存储库的可用工具
  * 使工具能够通过代理自己的模块从代理存储库加载
  * 记录了 LLM 调用的工具使用情况

  #### 新功能与增强

  * 在 MCP 集成中添加了 streamable-http 传输支持
  * 添加了对社区分析的支持
  * 通过 Gemini 示例扩展了 OpenAI 兼容部分
  * 引入了提示和内存系统的透明度功能
  * 工具发布的小幅增强

  #### 文档与指南

  * 为更好的导航大规模重构了文档
  * 扩展了 MCP 集成文档
  * 更新了内存文档和 README 视觉效果
  * 修复了异步启动示例中缺失的 await 关键字
  * 更新了 Portkey 和 Azure 嵌入文档
  * 在 LLM 指南中添加了企业测试图像
  * README 的常规更新
</Update>

<Update label="2025年5月27日">
  ## v0.121.1

  [在 GitHub 上查看发布](https://github.com/crewAIInc/crewAI/releases/tag/0.121.1)

  错误修复和更好的文档
</Update>

<Update label="2025年5月22日">
  ## v0.121.0

  # 更新内容

  ## 核心改进与修复

  * 修复了创建工具时的编码错误
  * 修复了失败的 llama 测试
  * 更新了日志配置以保持一致性
  * 增强了遥测初始化和事件处理

  ## 新功能与增强

  * 向 Task 类添加了 markdown 属性
  * 向 Agent 类添加了 reasoning 属性
  * 为 Agent 添加了 inject_date 标志以实现自动日期注入
  * 实现了 HallucinationGuardrail（带有测试覆盖的空操作）

  ## 文档与指南

  * 为 StagehandTool 添加了文档并改进了 MDX 结构
  * 为 MCP 集成添加了文档并更新了企业文档
  * 记录了知识事件并更新了推理文档
  * 添加了 stop 参数文档
  * 修复了文档示例中的导入引用（before_kickoff、after_kickoff）
  * 常规文档更新和重组以提高清晰度
</Update>

<Update label="2025年5月15日">
  ## v0.120.1

  ## 新功能

  * 修复了带连字符的插值
</Update>

<Update label="2025年5月14日">
  ## v0.120.0

  ### 核心改进与修复

  * 默认启用完整的 Ruff 规则集以进行更严格的 linting
  * 使用上下文管理器解决了 FilteredStream 中的竞争条件
  * 修复了代理知识重置问题
  * 将代理获取逻辑重构为实用工具模块

  ### 新功能与增强

  * 添加了从存储库直接加载代理的支持
  * 启用了为任务设置空上下文
  * 增强了代理存储库反馈并修复了工具自动导入行为
  * 引入了知识的直接初始化（绕过 knowledge_sources）

  ### 文档与指南

  * 为当前安全实践更新了 security.md
  * 为清晰起见清理了 Google 设置部分
  * 在输入 Gemini 密钥时添加了指向 AI Studio 的链接
  * 更新了 Arize Phoenix 可观察性指南
  * 刷新了流程文档
</Update>

<Update label="2025年5月8日">
  ## v0.119.0

  更新内容

  ## 核心改进与修复

  * 通过增强 pytest 处理不可靠测试来提高测试可靠性
  * 修复了嵌入维度不匹配时记忆重置崩溃的问题
  * 为 Crew 和 LiteAgent 启用了父流程识别
  * 防止遥测在不可用时相关崩溃
  * 升级了 LiteLLM 版本以实现更好的兼容性
  * 通过删除 skip_external_api 修复了 llama 转换器测试

  ## 新功能与增强

  * 在代理中引入了知识检索提示重写，以改进跟踪和调试
  * 使 LLM 设置和快速入门指南与模型无关

  ## 文档与指南

  * 为 RAG 工具添加了高级配置文档
  * 更新了 Windows 故障排除指南
  * 改进了文档示例以获得更好的清晰度
  * 修复了文档和配置文件中的拼写错误
</Update>

<Update label="2025年4月30日">
  ## v0.118.0

  ### 核心改进与修复

  * 修复了缺失提示或系统模板的问题。
  * 删除了全局日志配置以避免意外的覆盖。
  * 将 TaskGuardrail 重命名为 LLMGuardrail 以提高清晰度。
  * 为兼容性将 litellm 降级到版本 1.167.1。
  * 添加了缺失的 **init**.py 文件以确保正确的模块初始化。

  ### 新功能与增强

  * 添加了无代码 Guardrail 创建支持，以简化 AI 行为控制。

  ### 文档与指南

  * 从公共文档中删除了 CrewStructuredTool 以反映内部用法。
  * 更新了企业文档和 YouTube 嵌入以改进入职体验。
</Update>

<Update label="2025年4月28日">
  ## v0.117.1

  * 构建：升级 crewai-tools
  * 将 liteLLM 升级到最新版本
  * 修复 Mem0 OSS
</Update>

<Update label="2025年4月28日">
  ## v0.117.0

  # 更新内容

  ## 新功能与增强

  * 在 `@tool` 装饰器中添加了 `result_as_answer` 参数支持。
  * 引入了对新语言模型的支持：GPT-4.1、Gemini-2.0 和 Gemini-2.5 Pro。
  * 增强了知识管理功能。
  * 在 CLI 中添加了 Huggingface 提供程序选项。
  * 改进了 Python 3.10+ 的兼容性和 CI 支持。

  ## 核心改进与修复

  * 修复了错误的模板参数和缺失输入的问题。
  * 通过协程条件检查改进了异步流处理。
  * 通过隔离配置和正确的记忆对象复制增强了内存管理。
  * 通过正确的引用修复了轻量级代理的初始化。
  * 解决了 Python 类型提示问题并删除了冗余导入。
  * 更新了事件位置以改进工具使用跟踪。
  * 当流程失败时引发明确的异常。
  * 从各种模块中删除了未使用的代码和冗余注释。
  * 将 GitHub App 令牌操作更新到 v2。

  ## 文档与指南

  * 增强了文档结构，包括企业部署说明。
  * 为文档生成自动创建输出文件夹。
  * 修复了 `WeaviateVectorSearchTool` 文档中的损坏链接。
  * 修复了 Guardrail 文档用法和 JSON 搜索工具的导入路径。
  * 更新了 `CodeInterpreterTool` 的文档。
  * 改进了 SEO、上下文导航和文档页面的错误处理。
</Update>

<Update label="2025年4月10日">
  ## v0.114.0

  # 更新内容

  ## 新功能与增强

  * 代理作为原子单位。（`Agent(...).kickoff()`）
  * 支持自定义 LLM 实现。
  * 集成了外部内存和 Opik 可观察性。
  * 增强了 YAML 提取。
  * 多模态代理验证。
  * 为代理和团队添加了安全指纹。

  ## 核心改进与修复

  * 改进了序列化、代理复制和 Python 兼容性。
  * 为 emit() 添加了通配符支持。
  * 添加了对额外路由器调用和上下文窗口调整的支持。
  * 修复了类型问题、验证和导入语句。
  * 改进了方法性能。
  * 增强了代理任务处理、事件发射和内存管理。
  * 修复了 CLI 问题、条件任务、克隆行为和工具输出。

  ## 文档与指南

  * 改进了文档结构、主题和组织。
  * 添加了本地 NVIDIA NIM 与 WSL2、W\&B Weave 和 Arize Phoenix 的指南。
  * 更新了工具配置示例、提示和可观察性文档。
  * 在流程中使用单个代理的指南
</Update>

<Update label="2025年3月17日">
  ## v0.108.0

  # 功能

  * 在 PR #2190 中将 crew\.py 模板中的制表符转换为空格
  * 在 PR #2266 中增强了 LLM 流式响应处理和事件系统
  * 在 PR #2310 中包含了 model_name
  * 在 PR #2321 中增强了事件侦听器，具有丰富的可视性和改进的日志记录
  * 在 PR #2332 中添加了指纹

  # 错误修复

  * 在 PR #2308 中修复了 Mistral 问题
  * 在 PR #2370 中修复了文档中的错误
  * 在 PR #2369 中修复了指纹属性的类型检查错误

  # 文档更新

  * 在 PR #2259 中改进了工具文档
  * 在 PR #2196 中更新了 uv 工具包的安装指南
  * 在 PR #2363 中添加了使用 uv 工具升级 crewAI 的说明
  * 在 PR #2254 中添加了 ApifyActorsTool 文档
</Update>

<Update label="2025年3月9日">
  ## v0.105.0

  **核心改进与修复**

  * 修复了缺失模板变量和用户内存配置的问题。
  * 改进了异步流支持并解决了代理响应格式问题。
  * 增强了记忆重置功能并修复了 CLI 内存命令。
  * 修复了类型问题、工具调用属性和遥测解耦。

  **新功能与增强**

  * 添加了流程状态导出并改进了状态实用工具。
  * 通过可选的团队嵌入器增强了代理知识设置。
  * 引入了事件发射器以实现更好的可观察性和 LLM 调用跟踪。
  * 添加了对 Python 3.10 和来自 langchain_ollama 的 ChatOllama 的支持。
  * 为 o3-mini 模型集成了上下文窗口大小支持。
  * 添加了对多个路由器调用的支持。

  **文档与指南**

  * 改进了文档布局和层次结构。
  * 添加了 QdrantVectorSearchTool 指南并阐明了事件侦听器用法。
  * 修复了提示中的拼写错误并更新了 Amazon Bedrock 模型列表。
</Update>

<Update label="2025年2月13日">
  ## v0.102.0

  ### 核心改进与修复

  * 增强的 LLM 支持：改进了 Anthropic 模型的结构化 LLM 输出、参数处理和格式化。
  * 团队和代理稳定性：修复了使用知识源克隆代理/团队、条件任务中的多个任务输出以及被忽略的团队任务回调的问题。
  * 内存和存储修复：修复了 Bedrock 的短期内存处理，确保了正确的嵌入器初始化，并在团队类中添加了重置记忆功能。
  * 训练和执行可靠性：修复了损坏的训练以及字典和列表输入类型的插值问题。

  ### 新功能与增强

  * 高级知识管理：改进了命名约定并通过自定义嵌入器支持增强了嵌入配置。
  * 扩展的日志记录和可观察性：为日志记录添加了 JSON 格式支持，并集成了 MLflow 跟踪文档。
  * 数据处理改进：更新了 excel_knowledge_source.py 以处理多标签文件。
  * 一般性能和代码库清理：简化了企业代码对齐并解决了 linting 问题。
  * 添加新工具 QdrantVectorSearchTool

  ### 文档与指南

  * 更新了 AI 和内存文档：改进了 Bedrock、Google AI 和长期内存文档。
  * 任务和工作流程清晰度：向任务属性添加了"人工输入"行，Langfuse 指南和 FileWriterTool 文档。
  * 修复了各种拼写错误和格式问题。

  ### 维护和其他

  * 改进了 Google Docs 集成和当年的任务处理。
</Update>

<Update label="2025年1月28日">
  ## v0.100.0

  * 功能：添加 Composio 文档
  * 功能：添加 SageMaker 作为 LLM 提供程序
  * 修复：整体 LLM 连接问题
  * 修复：在训练中使用安全访问器
  * 修复：向 crew_chat.py 添加版本检查
  * 文档：crewai 聊天的新文档
  * 文档：改进 CLI 和 Composio 工具文档的格式和清晰度
</Update>

<Update label="2025年1月20日">
  ## v0.98.0

  * 功能：对话团队 v1
  * 功能：为流程状态添加唯一 ID
  * 功能：添加带有 FlowPersistence 接口的 @persist 装饰器
  * 集成：添加 SambaNova 集成
  * 集成：在 CLI 中添加 NVIDIA NIM 提供程序
  * 集成：引入 VoyageAI
  * 杂务：将模板中的日期更新为当年
  * 修复：修复 Mem0 集成中的 API 密钥行为和实体处理
  * 修复：修复了核心调用循环逻辑和相关测试
  * 修复：使工具输入成为实际对象而不是字符串
  * 修复：添加创建工具时缺失的重要部分
  * 修复：降低 litellm 版本以防止 Windows 问题
  * 修复：如果输入为 none，则在启动前
  * 修复：拼写错误
  * 修复：嵌套 pydantic 模型问题
  * 修复：Docling 问题
  * 修复：联合问题
  * 文档更新
</Update>

<Update label="2025年1月4日">
  ## v0.95.0

  * 功能：为团队添加多模态能力
  * 功能：编程式护栏
  * 功能：HITL 多轮
  * 功能：Gemini 2.0 支持
  * 功能：CrewAI 流程改进
  * 功能：添加工作流权限
  * 功能：添加对 langfuse 与 litellm 的支持
  * 功能：与 CrewAI 的 Portkey 集成
  * 功能：添加 interpolate_only 方法和改进的错误处理
  * 功能：Docling 支持
  * 功能：Weviate 支持
  * 修复：output_file 不遵循系统路径
  * 修复：重置短期内存时的磁盘 I/O 错误。
  * 修复：CrewJSONEncoder 现在接受枚举
  * 修复：Python 最大版本
  * 修复：任务中 output_file 的插值
  * 修复：正确处理同事角色名称的大小写/空格
  * 修复：将 tiktoken 添加为显式依赖项并记录 Rust 要求
  * 修复：在规划过程中包含代理知识
  * 修复：将 KnowledgeStorage 的存储初始化更改为 None
  * 修复：修复可选存储检查
  * 修复：在流程中包含事件发射器
  * 修复：文档字符串、错误处理和类型提示改进
  * 修复：抑制了来自 litellm pydantic 问题的 userWarnings
</Update>

<Update label="2024年12月5日">
  ## v0.86.0

  * 删除所有对管道和管道路由器的引用
  * 文档：在自定义 LLM 中将 Nvidia NIM 添加为提供程序
  * 添加知识演示 + 改进知识文档
  * Brandon/cre 509 hitl 多轮后续
  * 关于带有装饰器的 yaml 团队的新文档。简化模板团队
</Update>

<Update label="2024年12月4日">
  ## v0.85.0

  * 在代理级别添加了知识
  * 功能：移除 langchain
  * 改进了类型化的任务输出
  * 在 `crewai login` 时登录到工具存储库
  * 修复了结果作为答案未正确退出 LLM 循环的问题
  * 修复：在使用 ollama 提供程序运行时缺少密钥名称
  * 修复：发现的拼写问题
  * 更新运行 mypy 的自述文件
  * 向 mint.json 添加知识
  * 更新 Github 操作
  * 文档更新代理文档以包括创建代理的两种方法
  * 文档改进：LLM 配置和用法
</Update>

<Update label="2024年11月25日">
  ## v0.83.0

  * 新的 `before_kickoff` 和 `after_kickoff` 团队回调
  * 支持使用知识预先播种代理
  * 添加使用 Mem0 检索用户偏好和记忆的支持
  * 修复异步执行
  * 升级 chroma 并调整嵌入器功能生成器
  * 更新 CLI Watson 支持的模型 + 文档
  * 降低 Bandit 的级别
  * 修复所有测试
  * 更新文档
</Update>

<Update label="2024年11月14日">
  ## v0.80.0

  * 修复了令牌回调替换错误
  * 修复了步骤回调问题
  * 在使用指标上添加了缓存的提示令牌信息
  * 修复 crew_train_success 测试
</Update>

<Update label="2024年11月11日">
  ## v0.79.4

  * 一系列围绕 llms 支持的小错误修复
</Update>

<Update label="2024年11月10日">
  ## v0.79.0

  * 向流程添加输入
  * 增强日志存储以支持更多数据类型
  * 添加对 IBM 记忆的支持
  * 在 CLI 中将 Watson 添加为选项
  * 添加 security.md 文件
  * 用 uv 环境变量替换 .netrc
  * 将 BaseTool 移动到主包并集中化工具描述生成
  * 如果 LLM 未返回响应则引发错误
  * 修复流程以支持循环并在测试中添加
  * 更新我们命名团队的方式并修复缺失的配置
  * 更新文档
</Update>

<Update label="2024年10月30日">
  ## v0.76.9

  * 将流程的绘图命令更新为 crewai flow plot
  * 添加 tomli 以便我们支持 3.10
  * 将安装命令选项转发到 `uv sync`
  * 改进工具文本描述和参数
  * 改进工具和流程文档
  * 更新流程 cli 以允许您使用 crewai flow add-crew 轻松地将其他团队添加到流程中
  * 修复了在使用多个 start 和 listen(and_(..., ..., ...)) 时的流程错误
</Update>

<Update label="2024年10月23日">
  ## v0.76.2

  * 更新 crewai create 命令
</Update>

<Update label="2024年10月23日">
  ## v0.76.0

  * 修复/修复缺失的 API 提示 + CLI 文档更新
  * 杂务（自述文件）：修复贡献中"运行测试"的步骤
  * 支持不安全的代码执行。在 docker 安装中添加并运行检查
  * 修复嵌入函数的记忆导入
</Update>

<Update label="2024年10月23日">
  ## v0.75.1

  * crewai crewat 上的新 `--provider` 选项
</Update>

<Update label="2024年10月23日">
  ## v0.75.0

  * 修复训练后测试
  * 简化流程
  * 调整 `crewai tool install <tool>`
  * 确保原始嵌入配置工作
  * 修复错误
  * 更新文档 - 包括向 LLM 文档添加 Cerebras LLM 示例配置
  * 删除不必要的测试
</Update>

<Update label="2024年10月18日">
  ## v0.74.2

  * 功能：将 poetry.lock 添加到 uv 迁移
  * 修复工具调用问题
</Update>

<Update label="2024年10月18日">
  ## v0.74.0

  * UV 迁移
  * 将工具 CLI 调整到 UV
  * 添加从 Poetry -> UV 的警告
  * CLI 以允许模型选择和提交 API 密钥
  * 新的记忆基础
  * 修复 Linting 和警告
  * 更新文档
  * 错误修复
</Update>

<Update label="2024年10月11日">
  ## v0.70.1

  * 新流程功能
  * 流程可视化工具
  * 创建 `crewai create flow` 命令
  * 创建 `crewai tool create <tool>` 命令
  * 为发布工具添加 Git 验证
  * 修复：JSON 编码日期对象
  * 新文档
  * 更新 README
  * 错误修复
</Update>

<Update label="2024年9月27日">
  ## v0.65.2

  * 添加实验性流程功能
  * 修复任务顺序错误
  * 更新模板
</Update>

<Update label="2024年9月27日">
  ## v0.64.0

  * 正确排序任务
  * 修复总结逻辑
  * 修复停止词逻辑
  * 将默认最大迭代次数增加到 20
  * 修复输入插值后的团队密钥
  * 修复训练功能
  * 添加初始工具 API
  * 拼写错误
  * 更新文档

  修复：#1359 #1355 #1353 #1356 等
</Update>

<Update label="2024年9月25日">
  ## v0.63.6

  * 更新项目模板
</Update>

<Update label="2024年9月25日">
  ## v0.63.5

  * 恢复对 o1 系列的支持，以及任何不支持停止词的模型
  * 更新依赖项
  * 更新日志
  * 更新文档
</Update>

<Update label="2024年9月24日">
  ## v0.63.2

  * 添加 OPENAI_BASE_URL 作为后备
  * 添加正确的 LLM 导入
  * 更新文档
</Update>

<Update label="2024年9月24日">
  ## v0.63.1

  * 支持未来 CrewAI 部署的小错误修复
</Update>

<Update label="2024年9月24日">
  ## v0.63.0

  * 新的 LLM 类以与 LLM 交互（利用 LiteLLM）
  * 添加对自定义记忆接口的支持
  * 将 GPT-4o-mini 带为默认模型
  * 更新文档
  * 更新依赖项
  * 错误修复
    * 删除 `kickoff_for_each_async` 中的冗余任务创建
</Update>

<Update label="2024年9月18日">
  ## v0.61.0

  * 更新依赖项
  * 以不同颜色打印最大 rpm 消息
  * 更新测试的所有盒带
  * 始终以用户消息结束 - 以更好地支持某些模型（如 bedrock 模型）
  * 整体小错误修复
</Update>

<Update label="2024年9月16日">
  ## v0.60.0

  * 移除 LangChain 并重建执行器
  * 使所有测试恢复绿色
  * 添加不使用系统提示的功能 use_system_prompt 在代理上
  * 添加不使用停止词的功能（支持 o1 模型）use_stop_words 在代理上
  * 滑动上下文窗口重命名为 respect_context_window，并默认启用
  * 委托现在默认禁用
  * 内部提示也略有改变
  * 整体可靠性和结果质量
  * 新支持：
    * 每分钟最大请求数
    * 在给出最终答案之前的最大迭代次数
    * 充分利用系统提示
    * 令牌计算流程
    * 团队和代理执行的新日志记录
</Update>

<Update label="2024年9月13日">
  ## v0.55.2

  * 添加自动完成功能
  * 将名称和 expected_output 添加到 TaskOutput
  * 新的 `crewai install` CLI
  * 新的 `crewai deploy` CLI
  * 清理管道功能
  * 更新了文档
  * 开发体验改进，如 bandit CI 管道
  * 修复错误：
    * 能够使用 `planning_llm`
    * 修复基于 YAML 的项目
    * 修复 Azure 支持
    * 添加对 Python 3.10 的支持
    * 远离 Pydantic v1
</Update>

<Update label="2024年8月11日">
  ## v0.51.0

  * crewAI 测试/评估 - [https://docs.crewai.com/core-concepts/Testing/](https://docs.crewai.com/core-concepts/Testing/)
  * 添加新的滑动上下文窗口
  * 允许 YAML 上的所有属性 - [https://docs.crewai.com/getting-started/Start-a-New-CrewAI-Project-Template-Method/#customizing-your-project](https://docs.crewai.com/getting-started/Start-a-New-CrewAI-Project-Template-Method/#customizing-your-project)
  * 添加初始管道结构 - [https://docs.crewai.com/core-concepts/Pipeline/](https://docs.crewai.com/core-concepts/Pipeline/)
  * 能够为规划步骤设置 LLM - [https://docs.crewai.com/core-concepts/Planning/](https://docs.crewai.com/core-concepts/Planning/)
  * 新的团队运行命令 - [https://docs.crewai.com/getting-started/Start-a-New-CrewAI-Project-Template-Method/#running-your-project](https://docs.crewai.com/getting-started/Start-a-New-CrewAI-Project-Template-Method/#running-your-project)
  * 现在保存文件将字典转储到 JSON - [https://docs.crewai.com/core-concepts/Tasks/#creating-directories-when-saving-files](https://docs.crewai.com/core-concepts/Tasks/#creating-directories-when-saving-files)
  * 对工具输出使用详细设置
  * 添加了新的 Github 模板
  * 新的视觉工具 - [https://docs.crewai.com/tools/VisionTool/](https://docs.crewai.com/tools/VisionTool/)
  * 新的 DALL-E 工具 - [https://docs.crewai.com/tools/DALL-ETool/](https://docs.crewai.com/tools/DALL-ETool/)
  * 新的 MySQL 工具 - [https://docs.crewai.com/tools/MySQLTool/](https://docs.crewai.com/tools/MySQLTool/)
  * 新的 NL2SQL 工具 - [https://docs.crewai.com/tools/NL2SQLTool.md](https://docs.crewai.com/tools/NL2SQLTool.md)
  * 错误修复：
    * 规划功能输出的错误
    * 层次流程的异步任务
    * 非 OAI 模型的更好 pydantic 输出
    * JSON 截断问题
    * 修复日志类型
    * 仅在设置了 Env 密钥时才导入 AgentOps
    * 清理代理角色以确保有效的目录名称（Windows）
    * 工具名称不应包含 OpenAI 的空格
    * 一堆小问题
</Update>

<Update label="2024年7月20日">
  ## v0.41.1

  * 修复规划功能的错误
</Update>

<Update label="2024年7月19日">
  ## v0.41.0

  * **[重大更改]** 类型安全输出
    * 所有团队和任务现在返回适当的对象 TaskOuput 和 CrewOutput
  * **[功能]** 团队的新规划功能（先规划后执行）
    * 通过在团队实例中添加 planning=True
  * **[功能]** 引入重播功能
    * 新的 CLI 允许您列出上次运行的任务并从特定任务重播
  * **[功能]** 重置记忆的能力
    * 您可以在再次运行之前清理团队记忆
  * **[功能]** 为 LLM 调用添加重试功能
    * 您可以重试 llm 调用而不停止团队执行
  * **[功能]** 添加了自定义转换器的能力
  * **[工具]** 使用类型提示和新属性增强工具
  * **[工具]** 添加了 MultiON 工具
  * **[工具]** 修复了文件抓取工具
  * **[工具]** 修复了抓取工具中的错误
  * **[工具]** 将 crewAI-tools 依赖项提升到版本
  * **[错误]** 一般错误修复和改进
  * **[错误]** 遥测修复
  * **[错误]** 拼写检查更正
  * **[文档]** 更新了文档
</Update>

<Update label="2024年7月6日">
  ## v0.36.0

  * 错误修复
  * 更新文档
  * 更新本机提示
  * 修复提示中的拼写错误
  * 添加 AgentOps 本机支持
  * 添加 Firecrawl 工具
  * 添加新功能以将工具结果作为代理结果返回
  * 改进编码解释器工具
  * 添加新选项以创建自己的转换器类（文档待定）
</Update>

<Update label="2024年7月4日">
  ## v0.35.8

  * 修复 embechain 依赖问题
</Update>

<Update label="2024年7月2日">
  ## v0.35.7

  * 新的 @composiohq 集成已发布
  * 文档更新
  * 自定义 GPT 已更新
  * 调整管理器详细级别
  * 错误修复
</Update>

<Update label="2024年7月1日">
  ## v0.35.5

  * 修复 embedchain 依赖项
</Update>

<Update label="2024年7月1日">
  ## v0.35.4

  * 更新 crewai create CLI 以使用新版本
</Update>

<Update label="2024年7月1日">
  ## v0.35.3

  * 代码执行错误已修复
  * 更新整体文档
  * 提升 crewai-tools 的版本
  * 提升许多依赖项的版本
  * 整体错误修复
</Update>

<Update label="2024年6月29日">
  ## v0.35.0

  * 您的代理现在可以执行代码
  * 带来任何第 3 方代理，LlamaIndex、LangChain 和 Autogen 代理现在都可以成为您团队的一部分！
  * 在执行之前训练您的团队并获得一致的结果！新的 CLI `crewai train -n X`
  * 错误修复和文档更新（仍然缺少一些新的文档更新即将推出）
</Update>

<Update label="2024年6月22日">
  ## v0.32.2

  * 将 `crewai create` CLI 更新为使用新版本
  * 修复委托代理匹配
</Update>

<Update label="2024年6月21日">
  ## v0.32.0

  * 新的 `kickoff_for_each`、`kickoff_async` 和 `kickoff_for_each_async` 方法，以更好地控制启动过程
  * 添加对所有 LlamaIndex 集成中心的支持
  * 将 `usage_metrics` 添加到团队的完整输出
  * 在新的 YAML 格式中添加对多个团队的支持
  * 更新依赖项
  * 修复错误和拼写错误
  * 更新了文档
  * 在文档中添加了搜索
  * 将 gpt-4o 设为默认模型
  * 为 LangTrace、Browserbase 和 Exa Search 添加新文档
  * 向日志记录添加时间戳
</Update>

<Update label="2024年5月23日">
  ## v0.30.11

  * 更新项目生成模板
</Update>

<Update label="2024年5月14日">
  ## v0.30.8

  * 更新依赖项
  * crewAI 项目结构的小错误修复
  * 暂时删除自定义 YAML 解析器
</Update>

<Update label="2024年5月14日">
  ## v0.30.5

  * 使代理委托对于较小的模型更加通用
</Update>

<Update label="2024年5月13日">
  ## v0.30.4

  **文档更新将跟进** 对此表示抱歉，感谢您的耐心等待，我们即将发布新文档！

  ➿ 修复任务回调
  🧙  能够设置特定代理作为管理器，而不是让团队创建您的一个
  📄  能够设置系统、提示和响应模板，因此它与开源模型更可靠地配合使用（与较小的模型更好地配合使用）
  👨‍💻  改进 json 和 pydantic 输出（与较小的模型更好地配合使用）
  🔎 改进工具名称识别（与较小的模型更好地配合使用）
  🧰  改进工具使用（与较小的模型更好地配合使用）
  📃  带来自定义提示的初始支持
  2️⃣  修复重复令牌计算器指标
  🪚  添加了几个新工具，Browserbase 和 Exa Search
  📁  保存为文件时创建目录的能力
  🔁  更新依赖项 - 双重检查工具
  📄  整体小文档改进
  🐛  较小的错误修复（拼写错误等）
  👬  修复同事/协作者问题
  👀  较小的自述文件更新
</Update>

<Update label="2024年4月11日">
  ## v0.28.8

  * 更新 crewai CLI 使用的版本
</Update>

<Update label="2024年4月11日">
  ## v0.28.7

  * 错误修复
  * 使用错误修复更新了 crewAI 工具版本
</Update>

<Update label="2024年4月8日">
  ## v0.28.5

  * 主要长期记忆插值问题
  * 使用修复更新了工具包依赖项
  * 删除不必要的证书
</Update>

<Update label="2024年4月7日">
  ## v0.28.2

  * 主要长期记忆修复
</Update>

<Update label="2024年4月6日">
  ## v0.28.1

  * 将 crewai-tools 更新到 0.1.15
</Update>

<Update label="2024年4月5日">
  ## v0.28.0

  * 不覆盖 LLM 回调
  * 添加 `max_execution_time` 支持
  * 添加特定的记忆文档
  * 将工具使用日志记录颜色从黄色更改为紫色
  * 更新文档
</Update>

<Update label="2024年4月4日">
  ## v0.27.0

  * 🧠 **记忆（共享团队记忆）** - 要启用它，只需将 `memory=True` 添加到您的团队，它将透明地工作并使结果更好、更可靠，目前默认禁用
  * 🤚🏼 **本机人工输入支持：** [文档](https://docs.crewai.com/how-to/Human-Input-on-Execution/)
  * 🌐 **通用 RAG 工具支持：** 任何模型，不仅仅是 OpenAI。[示例](https://docs.crewai.com/tools/DirectorySearchTool/#custom-model-and-embeddings)
  * 🔍 **增强的缓存控制：** 见解巧妙的 cache_function 属性：[文档](https://docs.crewai.com/core-concepts/Tools/#custom-caching-mechanism)
  * 🔁 **更新的 crewai-tools 依赖项：** 始终与最新最好的保持同步。
  * ⛓️ **跨代理委托：** 代理之间更顺畅的合作。
  * 💠 **内部提示改进：** 更好的对话流程。
  * 📝 **通过更好的解析改进工具使用**
  * 🔒 **安全改进和更新依赖项**
  * 📄 **文档改进**
  * 🐛 **错误修复**
</Update>

<Update label="2024年3月12日">
  ## v0.22.5

  * 新模板上的其他小导入问题
</Update>

<Update label="2024年3月12日">
  ## v0.22.4

  修复模板问题
</Update>

<Update label="2024年3月11日">
  ## v0.22.2

  * 修复新 cli 模板上的错误
  * 保证新 cli 模板上的任务顺序
</Update>

<Update label="2024年3月11日">
  ## v0.22.0

  * 添加初始 CLI `crewai create` 命令
  * 添加代理和任务使用字典定义的能力
  * 添加更清晰的代理日志记录
  * 修复超出最大递归深度的错误
  * 修复文档
  * 更新 README
</Update>

<Update label="2024年3月4日">
  ## v0.19.0

  * 工具使用效率 +1023.21%
  * 平均使用的工具 +276%
  * 工具错误减少了 67%，比以往任何时候都更可靠。
  * 增强了委托能力
  * 通过设置 `function_calling_llm` 到代理或团队能够回退到函数调用
  * 能够在 `kickoff` 后通过 `crew.usage_metrics` 获取团队执行指标
  * 添加现在在启动时传递输入的能力 `crew.kickoff(inputs: {'key': 'value})`
  * 更新文档
</Update>

<Update label="2024年2月28日">
  ## v0.16.3

  * 修复整体错误
  * 确保代码向后兼容
</Update>

<Update label="2024年2月28日">
  ## v0.16.0

  * 删除 lingering `crewai_tools` 依赖项
  * 添加对输入插值的初始支持（缺少文档）
  * 添加跟踪工具使用、工具错误、格式化错误、令牌使用的能力
  * 更新 README
</Update>

<Update label="2024年2月26日">
  ## v0.14.4

  * 更新超时
  * 更新文档
  * 将 crewai_tools 移除为强制性
  * 默认使代理无记忆以减少令牌计数（对以前依赖于此的人来说是重大更改）
</Update>

<Update label="2024年2月24日">
  ## v0.14.3

  * 修复损坏的文档链接
  * 添加对没有工具的代理的支持
  * 避免空任务输出
</Update>

<Update label="2024年2月22日">
  ## v0.14.0

  v0.14.0rc 的所有改进。

  * 支持从开源模型导出 json 和 pydantic
</Update>

<Update label="2024年2月20日">
  ## v0.14.0rc

  * 添加对 crewai-tools 的支持
  * 添加支持将任务输出格式化为 Pydantic 对象或 JSON
  * 添加支持将任务输出保存到文件
  * 改进了代理间委托的可靠性
  * 改进了工具使用逻辑以正确使用函数调用
  * 更新内部提示
  * 支持没有参数的工具
  * 错误修复
</Update>

<Update label="2024年2月16日">
  ## v0.11.2

  * 添加进一步的错误日志记录，以便用户了解如果工具失败会发生什么
</Update>

<Update label="2024年2月16日">
  ## v0.11.1

  * 它修复了工具使用逻辑中的一个错误，该错误即使在用法中存在错误也提前缓存结果，阻止它再次使用该工具。
  * 它还将在红色中打印任何错误消息，允许用户了解工具的问题所在。
</Update>

<Update label="2024年2月13日">
  ## v0.11.0

  * 能够在整个团队和单个代理上设置 `function_calling_llm`
  * 一些早期的成本削减尝试
  * 改进函数调用以使用工具
  * 更新文档
</Update>

<Update label="2024年2月10日">
  ## v0.10.0

  * 能够从团队启动获取 `full_ouput` 以及所有任务输出
  * 能够为代理和团队设置 `step_callback` 函数，以便您可以获得所有中间步骤
  * 在一定数量的工具使用后，提醒代理预期的格式。
  * 新的工具使用内部现在使用 json，解锁了具有多个参数的工具
  * 重构整体委托逻辑，现在更可靠
  * 修复 `max_inter` 错误现在正确强制 llm 在达到该值时回答
  * 重建缓存结构，确保多个代理可以使用相同的缓存
  * 重构任务重复使用预防逻辑
  * 删除现在不必要的 `CrewAgentOutputParser`
  * 选择与 crewAI 团队共享完整的团队相关数据
  * 整体文档更新
</Update>

<Update label="2024年2月8日">
  ## v0.5.5

  * 整体文档 + 自述文件改进
  * 修复 RPM 控制器被不必要地设置
  * 添加早期阶段匿名遥测以进行库改进
</Update>

<Update label="2024年2月7日">
  ## v0.5.3

  * 层次管理器的快速修复
</Update>

<Update label="2024年2月6日">
  ## v0.5.2

  * 为层次流程添加 `manager_llm`
  * 改进 `max_inter` 和 `max_rpm` 逻辑
  * 更新 README 和文档
</Update>

<Update label="2024年2月4日">
  ## v0.5.0

  * 这个新版本为库带来了许多新功能和改进。

  ## 功能

  * 添加任务回调。
  * 添加对层次流程的支持。
  * 添加在另一个任务中引用特定任务的能力。
  * 添加并行任务执行的能力。

  ## 改进

  * 改进最大迭代和每分钟最大请求数。
  * 开发人员体验改进，文档字符串等。
  * 小的改进和拼写错误。
  * 修复静态类型错误。
  * 更新了 README 和文档。
</Update>

<Update label="2024年1月14日">
  ## v0.1.32

  * 迁移到 LangChain 0.1.0
  * 改进提示
  * 添加限制代理最大迭代次数的能力
  * 添加代理和团队的每分钟请求限制
  * 添加初始翻译支持
  * 添加希腊语翻译
  * 改进代码可读性
  * 使用 mkdocs 开始新文档
</Update>

<Update label="2024年1月7日">
  ## v0.1.23

  * 许多可靠性改进
  * 提示更改
  * 支持多种语言的初始更改
  * 修复任务重复执行的错误
  * 更好的执行错误处理
  * 更新自述文件
</Update>

<Update label="2023年12月30日">
  ## v0.1.14

  * 添加工具缓存和循环执行预防。(@joaomdmoura)
  * 为代理委托添加更多指南。(@joaomdmoura)
  * 更新以使用新的 openai 库版本。(@joaomdmoura)
  * 为记录器添加详细级别。(@joaomdmoura)
  * 删除 WIP 代码。(@joaomdmoura)
  * 许多开发人员生活质量改进（特别感谢 @greysonlalonde）。
  * 更新到 pydantic v2（也要特别感谢 @greysonlalonde）。
</Update>

<Update label="2023年11月24日">
  ## v0.1.2

  * 添加使用其他 LLM 的能力，而不仅仅是 OpenAI
</Update>

<Update label="2023年11月19日">
  ## v0.1.1

  # CrewAI v0.1.1 发布说明

  ## 新功能

  * **团队详细模式：** 现在允许您检查任务的执行情况。

  * **README 和文档更新：** 文档中的一系列小更新
</Update>

<Update label="2023年11月14日">
  ## v0.1.0

  # CrewAI v0.1.0 发布说明

  我们激动地宣布 CrewAI 的初始发布，版本 0.1.0！CrewAI 是一个旨在协调能够角色扮演和协作的自主 AI 代理的框架，以更有效地完成复杂任务。

  ## 新功能

  * **初始发布：** CrewAI 现已正式推出！这个基础性发布为 AI 代理的协同工作奠定了基础，每个代理都有自己的专门角色和目标。

  * **基于角色的代理设计：** 定义和定制具有特定角色、目标和成功所需工具的代理。

  * **代理间委托：** 代理现在配备自主委托任务的能力，实现团队间工作负载的动态分配。

  * **任务管理：** 动态创建和分配任务，灵活指定每个任务所需的工具。

  * **顺序流程：** 设置您的代理一个接一个地处理任务，确保有组织和可预测的工作流程。

  * **文档：** 开始探索 CrewAI，我们的初始文档将指导您完成框架的设置和使用。

  ## 增强

  * `Agent`、`Task`、`Crew` 和 `Process` 类的详细 API 文档。
  * 示例和教程，帮助您构建第一个 CrewAI 应用程序。
  * 代理间协作和委托机制的基本设置。

  ## 已知问题

  * 由于这是第一次发布，可能存在未发现的错误和优化领域。我们鼓励社区报告使用过程中发现的任何问题。

  ## 即将推出的功能

  * **高级流程管理：** 在未来的版本中，我们将引入更复杂的任务管理流程，包括协商和分层工作流程。
</Update>