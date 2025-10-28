# 自定义LLM实现

> 了解如何在CrewAI中创建自定义LLM实现。

## 概述

CrewAI通过`BaseLLM`抽象基类支持自定义LLM实现。这使你能够集成任何LiteLLM中没有内置支持的LLM提供商，或者实现自定义身份验证机制。

## 快速开始

以下是一个最小化的自定义LLM实现：

```python
from crewai import BaseLLM
from typing import Any, Dict, List, Optional, Union
import requests

class CustomLLM(BaseLLM):
    def __init__(self, model: str, api_key: str, endpoint: str, temperature: Optional[float] = None):
        # 重要：使用所需参数调用super().__init__()
        super().__init__(model=model, temperature=temperature)
      
        self.api_key = api_key
        self.endpoint = endpoint
      
    def call(
        self,
        messages: Union[str, List[Dict[str, str]]],
        tools: Optional[List[dict]] = None,
        callbacks: Optional[List[Any]] = None,
        available_functions: Optional[Dict[str, Any]] = None,
    ) -> Union[str, Any]:
        """使用给定消息调用LLM。"""
        # 如果需要，将字符串转换为消息格式
        if isinstance(messages, str):
            messages = [{"role": "user", "content": messages}]
      
        # 准备请求
        payload = {
            "model": self.model,
            "messages": messages,
            "temperature": self.temperature,
        }
      
        # 如果提供且支持，添加工具
        if tools and self.supports_function_calling():
            payload["tools"] = tools
      
        # 发起API调用
        response = requests.post(
            self.endpoint,
            headers={
                "Authorization": f"Bearer {self.api_key}",
                "Content-Type": "application/json"
            },
            json=payload,
            timeout=30
        )
        response.raise_for_status()
      
        result = response.json()
        return result["choices"][0]["message"]["content"]
      
    def supports_function_calling(self) -> bool:
        """如果你的LLM支持函数调用，则重写此方法。"""
        return True  # 如果你的LLM不支持工具，则改为False
      
    def get_context_window_size(self) -> int:
        """返回你的LLM的上下文窗口大小。"""
        return 8192  # 根据你的模型实际上下文窗口进行调整
```

## 使用你的自定义LLM

```python
from crewai import Agent, Task, Crew

# 假设你已经定义了上面的CustomLLM类
# 创建你的自定义LLM
custom_llm = CustomLLM(
    model="my-custom-model",
    api_key="your-api-key",
    endpoint="https://api.example.com/v1/chat/completions",
    temperature=0.7
)

# 与代理一起使用
agent = Agent(
    role="研究助理",
    goal="查找和分析信息",
    backstory="你是一名研究助理。",
    llm=custom_llm
)

# 创建并执行任务
task = Task(
    description="研究AI的最新发展",
    expected_output="全面的总结",
    agent=agent
)

crew = Crew(agents=[agent], tasks=[task])
result = crew.kickoff()
```

## 必需的方法

### 构造函数：`__init__()`

**关键**：你必须使用必需参数调用`super().__init__(model, temperature)`：

```python
def __init__(self, model: str, api_key: str, temperature: Optional[float] = None):
    # 必需：使用model和temperature调用父构造函数
    super().__init__(model=model, temperature=temperature)
  
    # 你的自定义初始化
    self.api_key = api_key
```

### 抽象方法：`call()`

`call()`方法是你的LLM实现的核心。它必须：

* 接受消息（字符串或包含'role'和'content'的字典列表）
* 返回字符串响应
* 处理工具和函数调用（如果支持）
* 为错误引发适当的异常

### 可选方法

```python
def supports_function_calling(self) -> bool:
    """如果你的LLM支持函数调用，返回True。"""
    return True  # 默认为True

def supports_stop_words(self) -> bool:
    """如果你的LLM支持停止序列，返回True。"""
    return True  # 默认为True

def get_context_window_size(self) -> int:
    """返回上下文窗口大小。"""
    return 4096  # 默认为4096
```

## 常见模式

### 错误处理

```python
import requests

def call(self, messages, tools=None, callbacks=None, available_functions=None):
    try:
        response = requests.post(
            self.endpoint,
            headers={"Authorization": f"Bearer {self.api_key}"},
            json=payload,
            timeout=30
        )
        response.raise_for_status()
        return response.json()["choices"][0]["message"]["content"]
      
    except requests.Timeout:
        raise TimeoutError("LLM请求超时")
    except requests.RequestException as e:
        raise RuntimeError(f"LLM请求失败: {str(e)}")
    except (KeyError, IndexError) as e:
        raise ValueError(f"无效的响应格式: {str(e)}")
```

### 自定义身份验证

```python
from crewai import BaseLLM
from typing import Optional

class CustomAuthLLM(BaseLLM):
    def __init__(self, model: str, auth_token: str, endpoint: str, temperature: Optional[float] = None):
        super().__init__(model=model, temperature=temperature)
        self.auth_token = auth_token
        self.endpoint = endpoint
  
    def call(self, messages, tools=None, callbacks=None, available_functions=None):
        headers = {
            "Authorization": f"Custom {self.auth_token}",  # 自定义身份验证格式
            "Content-Type": "application/json"
        }
        # 其余实现...
```

### 停止词支持

CrewAI自动添加`"\nObservation:"`作为停止词来控制代理行为。如果你的LLM支持停止词：

```python
def call(self, messages, tools=None, callbacks=None, available_functions=None):
    payload = {
        "model": self.model,
        "messages": messages,
        "stop": self.stop  # 在API调用中包含停止词
    }
    # 发起API调用...

def supports_stop_words(self) -> bool:
    return True  # 你的LLM支持停止序列
```

如果你的LLM本身不支持停止词：

```python
def call(self, messages, tools=None, callbacks=None, available_functions=None):
    response = self._make_api_call(messages, tools)
    content = response["choices"][0]["message"]["content"]
  
    # 手动在停止词处截断
    if self.stop:
        for stop_word in self.stop:
            if stop_word in content:
                content = content.split(stop_word)[0]
                break
  
    return content

def supports_stop_words(self) -> bool:
    return False  # 告诉CrewAI我们手动处理停止词
```

## 函数调用

如果你的LLM支持函数调用，实现完整的流程：

```python
import json

def call(self, messages, tools=None, callbacks=None, available_functions=None):
    # 将字符串转换为消息格式
    if isinstance(messages, str):
        messages = [{"role": "user", "content": messages}]
  
    # 发起API调用
    response = self._make_api_call(messages, tools)
    message = response["choices"][0]["message"]
  
    # 检查函数调用
    if "tool_calls" in message and available_functions:
        return self._handle_function_calls(
            message["tool_calls"], messages, tools, available_functions
        )
  
    return message["content"]

def _handle_function_calls(self, tool_calls, messages, tools, available_functions):
    """使用正确的消息流处理函数调用。"""
    for tool_call in tool_calls:
        function_name = tool_call["function"]["name"]
      
        if function_name in available_functions:
            # 解析并执行函数
            function_args = json.loads(tool_call["function"]["arguments"])
            function_result = available_functions[function_name](**function_args)
          
            # 将函数调用和结果添加到消息历史中
            messages.append({
                "role": "assistant",
                "content": None,
                "tool_calls": [tool_call]
            })
            messages.append({
                "role": "tool",
                "tool_call_id": tool_call["id"],
                "name": function_name,
                "content": str(function_result)
            })
          
            # 使用更新的上下文再次调用LLM
            return self.call(messages, tools, None, available_functions)
  
    return "函数调用失败"
```

## 故障排除

### 常见问题

**构造函数错误**

```python
# ❌ 错误 - 缺少必需参数
def __init__(self, api_key: str):
    super().__init__()

# ✅ 正确
def __init__(self, model: str, api_key: str, temperature: Optional[float] = None):
    super().__init__(model=model, temperature=temperature)
```

**函数调用不工作**

* 确保`supports_function_calling()`返回`True`
* 检查你是否在响应中处理了`tool_calls`
* 验证`available_functions`参数是否正确使用

**身份验证失败**

* 验证API密钥格式和权限
* 检查身份验证头格式
* 确保端点URL正确

**响应解析错误**

* 在访问嵌套字段之前验证响应结构
* 处理content可能为None的情况
* 为格式错误的响应添加适当的错误处理

## 测试你的自定义LLM

```python
from crewai import Agent, Task, Crew

def test_custom_llm():
    llm = CustomLLM(
        model="test-model",
        api_key="test-key",
        endpoint="https://api.test.com"
    )
  
    # 测试基本调用
    result = llm.call("Hello, world!")
    assert isinstance(result, str)
    assert len(result) > 0
  
    # 使用CrewAI代理进行测试
    agent = Agent(
        role="测试代理",
        goal="测试自定义LLM",
        backstory="一个测试代理。",
        llm=llm
    )
  
    task = Task(
        description="打个招呼",
        expected_output="一个问候",
        agent=agent
    )
  
    crew = Crew(agents=[agent], tasks=[task])
    result = crew.kickoff()
    assert "hello" in result.raw.lower()
```

本指南涵盖了在CrewAI中实现自定义LLM的基本要素。