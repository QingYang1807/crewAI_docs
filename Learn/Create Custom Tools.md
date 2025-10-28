# 创建自定义工具

> 全面指南：在 CrewAI 框架中创建、使用和管理自定义工具，包括新功能及错误处理。

## 在 CrewAI 中创建和使用工具

本指南提供了为 CrewAI 框架创建自定义工具的详细说明，以及如何高效地管理和使用这些工具，包含工具委托、错误处理和动态工具调用等最新功能。同时强调了协作工具的重要性，使代理能够执行各种操作。

### 继承 `BaseTool`

要创建个性化工具，需要继承 `BaseTool` 并定义必要属性，包括用于输入验证的 `args_schema` 和 `_run` 方法。

```python Code theme={null}
from typing import Type
from crewai.tools import BaseTool
from pydantic import BaseModel, Field

class MyToolInput(BaseModel):
    """MyCustomTool 的输入模式。"""
    argument: str = Field(..., description="参数的描述。")

class MyCustomTool(BaseTool):
    name: str = "我的工具名称"
    description: str = "该工具的功能。这对有效使用至关重要。"
    args_schema: Type[BaseModel] = MyToolInput

    def _run(self, argument: str) -> str:
        # 在此处编写工具逻辑
        return "工具的结果"
```

### 使用 `tool` 装饰器

或者，您可以使用 `@tool` 装饰器。这种方法允许您直接在函数内部定义工具的属性和功能，为创建满足需求的专门工具提供了一种简洁高效的方式。

```python Code theme={null}
from crewai.tools import tool

@tool("工具名称")
def my_simple_tool(question: str) -> str:
    """清晰的工具描述。"""
    # 工具逻辑在此处
    return "工具输出"
```

### 为工具定义缓存函数

要通过缓存优化工具性能，可使用 `cache_function` 属性定义自定义缓存策略。

```python Code theme={null}
@tool("带缓存的工具")
def cached_tool(argument: str) -> str:
    """工具功能描述。"""
    return "可缓存的结果"

def my_cache_strategy(arguments: dict, result: str) -> bool:
    # 定义自定义缓存逻辑
    return True if some_condition else False

cached_tool.cache_function = my_cache_strategy
```

通过遵循这些指南，并将新功能和协作工具整合到您的工具创建和管理流程中，您可以充分利用 CrewAI 框架的全部功能，提升开发体验和 AI 代理的效率。