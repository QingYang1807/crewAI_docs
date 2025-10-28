# Bedrock 知识库检索器

> 使用自然语言查询从 Amazon Bedrock 知识库中检索信息

# `BedrockKBRetrieverTool`

`BedrockKBRetrieverTool` 使 CrewAI 代理能够使用自然语言查询从 Amazon Bedrock 知识库中检索信息。

## 安装

```bash  theme={null}
uv pip install 'crewai[tools]'
```

## 要求

* 已配置 AWS 凭证（通过环境变量或 AWS CLI）
* `boto3` 和 `python-dotenv` 包
* 可访问 Amazon Bedrock 知识库

## 用法

以下是如何将工具与 CrewAI 代理一起使用：

```python {2, 4-17} theme={null}
from crewai import Agent, Task, Crew
from crewai_tools.aws.bedrock.knowledge_base.retriever_tool import BedrockKBRetrieverTool

# 初始化工具
kb_tool = BedrockKBRetrieverTool(
    knowledge_base_id="your-kb-id",
    number_of_results=5
)

# 创建一个使用该工具的 CrewAI 代理
researcher = Agent(
    role='Knowledge Base Researcher',
    goal='Find information about company policies',
    backstory='I am a researcher specialized in retrieving and analyzing company documentation.',
    tools=[kb_tool],
    verbose=True
)

# 为代理创建任务
research_task = Task(
    description="Find our company's remote work policy and summarize the key points.",
    agent=researcher
)

# 创建包含代理的团队
crew = Crew(
    agents=[researcher],
    tasks=[research_task],
    verbose=2
)

# 运行团队
result = crew.kickoff()
print(result) 
```

## 工具参数

| 参数                     | 类型   | 必需   | 默认值 | 描述                                                                      |
| :----------------------- | :----- | :----- | :----- | :------------------------------------------------------------------------ |
| **knowledge\_base\_id**  | `str`  | 是     | None   | 知识库的唯一标识符（0-10 个字母数字字符）                                 |
| **number\_of\_results**  | `int`  | 否     | 5      | 要返回的最大结果数                                                        |
| **retrieval\_configuration** | `dict` | 否     | None   | 知识库查询的自定义配置                                                     |
| **guardrail\_configuration** | `dict` | 否     | None   | 内容过滤设置                                                               |
| **next\_token**          | `str`  | 否     | None   | 分页令牌                                                                   |

## 环境变量

```bash  theme={null}
BEDROCK_KB_ID=your-knowledge-base-id  # 作为 knowledge_base_id 的替代方案
AWS_REGION=your-aws-region            # 默认为 us-east-1
AWS_ACCESS_KEY_ID=your-access-key     # AWS 身份验证所必需
AWS_SECRET_ACCESS_KEY=your-secret-key # AWS 身份验证所必需
```

## 响应格式

该工具以 JSON 格式返回结果：

```json  theme={null}
{
  "results": [
    {
      "content": "检索到的文本内容",
      "content_type": "text",
      "source_type": "S3",
      "source_uri": "s3://bucket/document.pdf",
      "score": 0.95,
      "metadata": {
        "additional": "metadata"
      }
    }
  ],
  "nextToken": "分页令牌",
  "guardrailAction": "NONE"
}
```

## 高级用法

### 自定义检索配置

```python  theme={null}
kb_tool = BedrockKBRetrieverTool(
    knowledge_base_id="your-kb-id",
    retrieval_configuration={
        "vectorSearchConfiguration": {
            "numberOfResults": 10,
            "overrideSearchType": "HYBRID"
        }
    }
)

policy_expert = Agent(
    role='Policy Expert',
    goal='Analyze company policies in detail',
    backstory='I am an expert in corporate policy analysis with deep knowledge of regulatory requirements.',
    tools=[kb_tool]
)
```

## 支持的数据源

* Amazon S3
* Confluence
* Salesforce
* SharePoint
* 网页
* 自定义文档位置
* Amazon Kendra
* SQL 数据库

## 使用场景

### 企业知识集成

* 使 CrewAI 代理能够访问您组织的专有知识，而无需暴露敏感数据
* 允许代理根据您公司的特定政策、程序和文档做出决策
* 创建能根据您的内部文档回答问题的代理，同时确保数据安全

### 专业领域知识

* 将 CrewAI 代理连接到特定领域的知识库（法律、医疗、技术），而无需重新训练模型
* 利用已在您的 AWS 环境中维护的现有知识库
* 将 CrewAI 的推理能力与您知识库中的领域特定信息相结合

### 数据驱动的决策制定

* 使 CrewAI 代理的响应基于您公司的实际数据，而非通用知识
* 确保代理根据您的特定业务背景和文档提供建议
* 通过从知识库中检索事实信息来减少幻觉

### 可扩展的信息访问

* 访问数 TB 的组织知识，而无需将其全部嵌入到您的模型中
* 仅动态查询特定任务所需的相关信息
* 利用 AWS 的可扩展基础设施高效处理大型知识库

### 合规与治理

* 确保 CrewAI 代理提供的响应符合您公司批准的文档
* 创建代理所使用信息来源的可审计跟踪
* 维护对代理可访问信息源的控制