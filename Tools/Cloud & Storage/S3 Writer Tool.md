# S3 写入工具

> `S3WriterTool` 使 CrewAI 代理能够将内容写入 Amazon S3 存储桶中的文件。

# `S3WriterTool`

## 描述

`S3WriterTool` 旨在将内容写入 Amazon S3 存储桶中的文件。此工具允许 CrewAI 代理在 S3 中创建或更新文件，使其非常适合需要存储数据、保存配置文件或将任何其他内容持久化到 AWS S3 存储的工作流程。

## 安装

要使用此工具，您需要安装所需的依赖项：

```shell theme={null}
uv add boto3
```

## 入门步骤

要有效使用 `S3WriterTool`，请按照以下步骤操作：

1. **安装依赖项**：使用上述命令安装所需的软件包。
2. **配置 AWS 凭证**：将您的 AWS 凭证设置为环境变量。
3. **初始化工具**：创建工具的实例。
4. **指定 S3 路径和内容**：提供您想要写入文件的 S3 路径和要写入的内容。

## 示例

以下示例演示如何使用 `S3WriterTool` 将内容写入 S3 存储桶中的文件：

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools.aws.s3 import S3WriterTool

# 初始化工具
s3_writer_tool = S3WriterTool()

# 定义一个使用该工具的代理
file_writer_agent = Agent(
    role="文件写入器",
    goal="将内容写入 S3 存储桶中的文件",
    backstory="一个在云存储中存储和管理文件的专家。",
    tools=[s3_writer_tool],
    verbose=True,
)

# 写入报告的示例任务
write_task = Task(
    description="生成季度销售数据的摘要报告并将其保存到 {my_bucket}。",
    expected_output="确认报告已成功保存到 S3。",
    agent=file_writer_agent,
)

# 创建并运行团队
crew = Crew(agents=[file_writer_agent], tasks=[write_task])
result = crew.kickoff(inputs={"my_bucket": "s3://my-bucket/reports/quarterly-summary.txt"})
```

## 参数

当代理使用 `S3WriterTool` 时，它接受以下参数：

* **file_path**：必需。S3 文件路径，格式为 `s3://bucket-name/file-name`。
* **content**：必需。要写入文件的内容。

## AWS 凭证

该工具需要 AWS 凭证才能访问 S3 存储桶。您可以使用环境变量配置这些凭证：

* **CREW_AWS_REGION**：您的 S3 存储桶所在的 AWS 区域。默认为 `us-east-1`。
* **CREW_AWS_ACCESS_KEY_ID**：您的 AWS 访问密钥 ID。
* **CREW_AWS_SEC_ACCESS_KEY**：您的 AWS 秘密访问密钥。

## 使用方法

将 `S3WriterTool` 与代理一起使用时，代理需要提供 S3 文件路径和要写入的内容：

```python Code theme={null}
# 与代理一起使用工具的示例
file_writer_agent = Agent(
    role="文件写入器",
    goal="将内容写入 S3 存储桶中的文件",
    backstory="一个在云存储中存储和管理文件的专家。",
    tools=[s3_writer_tool],
    verbose=True,
)

# 为代理创建一个写入特定文件的任务
write_config_task = Task(
    description="""
    创建具有以下数据库设置的配置文件：
    - host: db.example.com
    - port: 5432
    - username: app_user
    - password: secure_password
  
    将此配置保存为 JSON 文件到 {my_bucket}。
    """,
    expected_output="确认配置文件已成功保存到 S3。",
    agent=file_writer_agent,
)

# 运行任务
crew = Crew(agents=[file_writer_agent], tasks=[write_config_task])
result = crew.kickoff(inputs={"my_bucket": "s3://my-bucket/config/db-config.json"})
```

## 错误处理

`S3WriterTool` 包括对常见 S3 问题的错误处理：

* 无效的 S3 路径格式
* 权限问题（例如，没有对存储桶的写入权限）
* AWS 凭证问题
* 存储桶不存在

当发生错误时，该工具将返回包含问题详细信息的错误消息。

## 实现细节

`S3WriterTool` 使用适用于 Python 的 AWS 开发工具包 (boto3) 与 S3 交互：

```python Code theme={null}
class S3WriterTool(BaseTool):
    name: str = "S3 写入工具"
    description: str = "将内容写入给定 S3 文件路径的 Amazon S3 中的文件"
  
    def _run(self, file_path: str, content: str) -> str:
        try:
            bucket_name, object_key = self._parse_s3_path(file_path)

            s3 = boto3.client(
                's3',
                region_name=os.getenv('CREW_AWS_REGION', 'us-east-1'),
                aws_access_key_id=os.getenv('CREW_AWS_ACCESS_KEY_ID'),
                aws_secret_access_key=os.getenv('CREW_AWS_SEC_ACCESS_KEY')
            )

            s3.put_object(Bucket=bucket_name, Key=object_key, Body=content.encode('utf-8'))
            return f"成功将内容写入 {file_path}"
        except ClientError as e:
            return f"将文件写入 S3 时出错：{str(e)}"
```

## 结论

`S3WriterTool` 提供了一种将内容写入 Amazon S3 存储桶中文件的直接方法。通过使代理能够在 S3 中创建和更新文件，它促进了需要基于云的文件存储的工作流程。此工具对于数据持久化、配置管理、报告生成以及涉及在 AWS S3 存储中存储信息的任何任务特别有用。