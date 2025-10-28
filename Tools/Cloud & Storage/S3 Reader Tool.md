# S3 读取工具

> `S3ReaderTool` 使 CrewAI 智能体能够从 Amazon S3 存储桶中读取文件。

# `S3ReaderTool`

## 描述

`S3ReaderTool` 旨在从 Amazon S3 存储桶中读取文件。该工具允许 CrewAI 智能体访问和检索存储在 S3 中的内容，使其成为需要读取存储在 AWS S3 存储中的数据、配置文件或任何其他内容的工作流的理想选择。

## 安装

要使用此工具，您需要安装所需的依赖项：

```shell  theme={null}
uv add boto3
```

## 入门步骤

要有效使用 `S3ReaderTool`，请遵循以下步骤：

1.  **安装依赖项**：使用上述命令安装所需的包。
2.  **配置 AWS 凭证**：将您的 AWS 凭证设置为环境变量。
3.  **初始化工具**：创建工具的实例。
4.  **指定 S3 路径**：提供要读取的文件的 S3 路径。

## 示例

以下示例演示了如何使用 `S3ReaderTool` 从 S3 存储桶中读取文件：

```python Code theme={null}
from crewai import Agent, Task, Crew
from crewai_tools.aws.s3 import S3ReaderTool

# 初始化工具
s3_reader_tool = S3ReaderTool()

# 定义一个使用该工具的智能体
file_reader_agent = Agent(
    role="File Reader",
    goal="Read files from S3 buckets",
    backstory="An expert in retrieving and processing files from cloud storage.",
    tools=[s3_reader_tool],
    verbose=True,
)

# 读取配置文件的示例任务
read_task = Task(
    description="Read the configuration file from {my_bucket} and summarize its contents.",
    expected_output="A summary of the configuration file contents.",
    agent=file_reader_agent,
)

# 创建并运行团队
crew = Crew(agents=[file_reader_agent], tasks=[read_task])
result = crew.kickoff(inputs={"my_bucket": "s3://my-bucket/config/app-config.json"})
```

## 参数

`S3ReaderTool` 在被智能体使用时接受以下参数：

* **file\_path**：必需。S3 文件路径，格式为 `s3://bucket-name/file-name`。

## AWS 凭证

该工具需要 AWS 凭证才能访问 S3 存储桶。您可以使用环境变量配置这些凭证：

* **CREW\_AWS\_REGION**：您的 S3 存储桶所在的 AWS 区域。默认为 `us-east-1`。
* **CREW\_AWS\_ACCESS\_KEY\_ID**：您的 AWS 访问密钥 ID。
* **CREW\_AWS\_SEC\_ACCESS\_KEY**：您的 AWS 秘密访问密钥。

## 用法

将 `S3ReaderTool` 与智能体一起使用时，智能体需要提供 S3 文件路径：

```python Code theme={null}
# 将工具与智能体一起使用的示例
file_reader_agent = Agent(
    role="File Reader",
    goal="Read files from S3 buckets",
    backstory="An expert in retrieving and processing files from cloud storage.",
    tools=[s3_reader_tool],
    verbose=True,
)

# 为智能体创建一个读取特定文件的任务
read_config_task = Task(
    description="Read the application configuration file from {my_bucket} and extract the database connection settings.",
    expected_output="The database connection settings from the configuration file.",
    agent=file_reader_agent,
)

# 运行任务
crew = Crew(agents=[file_reader_agent], tasks=[read_config_task])
result = crew.kickoff(inputs={"my_bucket": "s3://my-bucket/config/app-config.json"})
```

## 错误处理

`S3ReaderTool` 包含对常见 S3 问题的错误处理：

* 无效的 S3 路径格式
* 文件丢失或无法访问
* 权限问题
* AWS 凭证问题

发生错误时，该工具将返回一条包含问题详细信息的错误消息。

## 实现细节

`S3ReaderTool` 使用 AWS SDK for Python (boto3) 与 S3 交互：

```python Code theme={null}
class S3ReaderTool(BaseTool):
    name: str = "S3 Reader Tool"
    description: str = "Reads a file from Amazon S3 given an S3 file path"
  
    def _run(self, file_path: str) -> str:
        try:
            bucket_name, object_key = self._parse_s3_path(file_path)

            s3 = boto3.client(
                's3',
                region_name=os.getenv('CREW_AWS_REGION', 'us-east-1'),
                aws_access_key_id=os.getenv('CREW_AWS_ACCESS_KEY_ID'),
                aws_secret_access_key=os.getenv('CREW_AWS_SEC_ACCESS_KEY')
            )

            # 从 S3 读取文件内容
            response = s3.get_object(Bucket=bucket_name, Key=object_key)
            file_content = response['Body'].read().decode('utf-8')

            return file_content
        except ClientError as e:
            return f"Error reading file from S3: {str(e)}"
```

## 结论

`S3ReaderTool` 提供了一种从 Amazon S3 存储桶读取文件的直接方法。通过使智能体能够访问存储在 S3 中的内容，它促进了需要基于云的文件访问的工作流。该工具对于数据处理、配置管理以及涉及从 AWS S3 存储中检索信息的任何任务都特别有用。