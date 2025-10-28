# 执行过程中的人工输入

> 在复杂决策过程中，将CrewAI与执行过程中的人工输入相结合，充分利用代理的属性和工具的全部功能。

## 代理执行中的人工输入

在几个代理执行场景中，人工输入至关重要，它允许代理在必要时请求额外信息或澄清。
这一功能在复杂决策过程中或当代理需要更多细节以有效完成任务时特别有用。

## 在CrewAI中使用人工输入

要将人工输入集成到代理执行中，请在任务定义中设置`human_input`标志。启用后，代理会在交付最终答案之前提示用户输入。
这种输入可以提供额外的上下文、澄清歧义或验证代理的输出。

### 示例：

```shell  theme={null}
pip install crewai
```

```python Code theme={null}
import os
from crewai import Agent, Task, Crew
from crewai_tools import SerperDevTool

os.environ["SERPER_API_KEY"] = "Your Key"  # serper.dev API key
os.environ["OPENAI_API_KEY"] = "Your Key"

# 加载工具
search_tool = SerperDevTool()

# 定义具有角色、目标、工具和额外属性的代理
researcher = Agent(
    role='高级研究分析师',
    goal='揭示AI和数据科学的前沿发展',
    backstory=(
        "你是一家领先科技智库的高级研究分析师。"
        "你的专长在于识别AI和数据科学中的新兴趋势和技术。"
        "你擅长剖析复杂数据并提出可行的见解。"
    ),
    verbose=True,
    allow_delegation=False,
    tools=[search_tool]
)
writer = Agent(
    role='技术内容策略师',
    goal='撰写关于技术进步的引人入胜的内容',
    backstory=(
        "你是一位著名的技术内容策略师，以关于技术和创新的深刻而引人入胜的文章而闻名。"
        "凭借对科技行业的深刻理解，你将复杂的概念转化为引人入胜的叙事。"
    ),
    verbose=True,
    allow_delegation=True,
    tools=[search_tool],
    cache=False,  # 为此代理禁用缓存
)

# 为代理创建任务
task1 = Task(
    description=(
        "对2025年AI的最新进展进行全面分析。"
        "识别关键趋势、突破性技术和潜在的行业影响。"
        "将你的发现汇编成详细报告。"
        "在最终确定答案之前，确保与人类核对草稿是否良好。"
    ),
    expected_output='关于2025年最新AI进展的全面完整报告，不要遗漏任何内容',
    agent=researcher,
    human_input=True
)

task2 = Task(
    description=(
        "利用研究人员的报告见解，撰写一篇引人入胜的博客文章，突出最重要的AI进展。"
        "你的文章应该既有信息量又易于理解，面向精通技术的受众。"
        "旨在捕捉这些突破的本质及其对未来影响的叙述。"
    ),
    expected_output='关于2025年最新AI进展的引人入胜的三段博客文章，格式为markdown',
    agent=writer,
    human_input=True
)

# 实例化具有顺序流程的团队
crew = Crew(
    agents=[researcher, writer],
    tasks=[task1, task2],
    verbose=True,
    memory=True,
    planning=True  # 为团队启用规划功能
)

# 让你的团队开始工作！
result = crew.kickoff()

print("######################")
print(result)
```