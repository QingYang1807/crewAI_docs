# 概述

> 与云服务、存储系统和基于云的AI平台交互

这些工具使您的代理能够与云服务交互、访问云存储，并利用基于云的AI平台进行可扩展的操作。

## **可用工具**

<CardGroup cols={2}>
  <Card title="S3读取工具" icon="cloud" href="/en/tools/cloud-storage/s3readertool">
    从Amazon S3存储桶读取文件和数据。
  </Card>

  <Card title="S3写入工具" icon="cloud-arrow-up" href="/en/tools/cloud-storage/s3writertool">
    将文件写入并上传到Amazon S3存储。
  </Card>

  <Card title="Bedrock调用代理" icon="aws" href="/en/tools/cloud-storage/bedrockinvokeagenttool">
    调用Amazon Bedrock代理以执行AI驱动的任务。
  </Card>

  <Card title="Bedrock知识库检索器" icon="database" href="/en/tools/cloud-storage/bedrockkbretriever">
    从Amazon Bedrock知识库中检索信息。
  </Card>
</CardGroup>

## **常见用例**

* **文件存储**：从云存储系统存储和检索文件
* **数据备份**：将重要数据备份到云存储
* **AI服务**：访问基于云的AI模型和服务
* **知识检索**：查询云托管的知识库
* **可扩展操作**：利用云基础设施进行处理

```python  theme={null}
from crewai_tools import S3ReaderTool, S3WriterTool, BedrockInvokeAgentTool

# 创建云工具
s3_reader = S3ReaderTool()
s3_writer = S3WriterTool()
bedrock_agent = BedrockInvokeAgentTool()

# 添加到您的代理
agent = Agent(
    role="云运营专员",
    tools=[s3_reader, s3_writer, bedrock_agent],
    goal="管理云资源和AI服务"
)
```