# LlamaIndex 工具

> `LlamaIndexTool` 是 LlamaIndex 工具和查询引擎的包装器。

# `LlamaIndexTool`

## 描述

`LlamaIndexTool` 设计为 LlamaIndex 工具和查询引擎的通用包装器，使您能够将 LlamaIndex 资源作为 RAG/智能代理流水线中的工具，插入到 CrewAI 代理中使用。此工具允许您将 LlamaIndex 强大的数据处理和检索功能无缝集成到 CrewAI 工作流中。

## 安装

要使用此工具，您需要安装 LlamaIndex：

```shell  theme={null}
uv add llama-index
```

## 入门步骤

要有效使用 `LlamaIndexTool`，请按照以下步骤操作：

1. **安装 LlamaIndex**：使用上述命令安装 LlamaIndex 包。
2. **设置 LlamaIndex**：遵循 [LlamaIndex 文档](https://docs.llamaindex.ai/) 设置 RAG/代理流水线。
3. **创建工具或查询引擎**：创建要与 CrewAI 一起使用的 LlamaIndex 工具或查询引擎。

## 示例

以下示例演示了如何从不同的 LlamaIndex 组件初始化工具：

### 从 LlamaIndex 工具创建

```python Code theme={null}
from crewai_tools import LlamaIndexTool
from crewai import Agent
from llama_index.core.tools import FunctionTool

# 示例 1：从 FunctionTool 初始化
def search_data(query: str) -> str:
    """在数据中搜索信息。"""
    # 您的实现代码
    return f"查询结果：{query}"

# 创建一个 LlamaIndex FunctionTool
og_tool = FunctionTool.from_defaults(
    search_data, 
    name="DataSearchTool",
    description="在数据中搜索信息"
)

# 用 LlamaIndexTool 包装它
tool = LlamaIndexTool.from_tool(og_tool)

# 定义一个使用该工具的代理
@agent
def researcher(self) -> Agent:
    '''
    这个代理使用 LlamaIndexTool 搜索信息。
    '''
    return Agent(
        config=self.agents_config["researcher"],
        tools=[tool]
    )
```

### 从 LlamaHub 工具创建

```python Code theme={null}
from crewai_tools import LlamaIndexTool
from llama_index.tools.wolfram_alpha import WolframAlphaToolSpec

# 从 LlamaHub 工具初始化
wolfram_spec = WolframAlphaToolSpec(app_id="your_app_id")
wolfram_tools = wolfram_spec.to_tool_list()
tools = [LlamaIndexTool.from_tool(t) for t in wolfram_tools]
```

### 从 LlamaIndex 查询引擎创建

```python Code theme={null}
from crewai_tools import LlamaIndexTool
from llama_index.core import VectorStoreIndex
from llama_index.core.readers import SimpleDirectoryReader

# 加载文档
documents = SimpleDirectoryReader("./data").load_data()

# 创建索引
index = VectorStoreIndex.from_documents(documents)

# 创建查询引擎
query_engine = index.as_query_engine()

# 从查询引擎创建 LlamaIndexTool
query_tool = LlamaIndexTool.from_query_engine(
    query_engine,
    name="Company Data Query Tool",
    description="使用此工具在公司文档中查找信息"
)
```

## 类方法

`LlamaIndexTool` 提供两个主要的类方法用于创建实例：

### from\_tool

从 LlamaIndex 工具创建 `LlamaIndexTool`。

```python Code theme={null}
@classmethod
def from_tool(cls, tool: Any, **kwargs: Any) -> "LlamaIndexTool":
    # 实现细节
```

### from\_query\_engine

从 LlamaIndex 查询引擎创建 `LlamaIndexTool`。

```python Code theme={null}
@classmethod
def from_query_engine(
    cls,
    query_engine: Any,
    name: Optional[str] = None,
    description: Optional[str] = None,
    return_direct: bool = False,
    **kwargs: Any,
) -> "LlamaIndexTool":
    # 实现细节
```

## 参数

`from_query_engine` 方法接受以下参数：

* **query\_engine**：必需。要包装的 LlamaIndex 查询引擎。
* **name**：可选。工具的名称。
* **description**：可选。工具的描述。
* **return\_direct**：可选。是否直接返回响应。默认为 `False`。

## 结论

`LlamaIndexTool` 提供了一种强大的方式来将 LlamaIndex 的功能集成到 CrewAI 代理中。通过包装 LlamaIndex 工具和查询引擎，它使代理能够利用复杂的数据检索和处理功能，增强其处理复杂信息源的能力。