# 内存系统

> 在CrewAI框架中利用内存系统来增强代理能力。

## 概述

CrewAI框架提供了一个复杂的内存系统，旨在显著增强AI代理的能力。CrewAI提供了**两种不同的内存方法**，适用于不同的用例：

1. **基本内存系统** - 内置的短期、长期和实体内存
2. **外部内存** - 独立的外部内存提供者

## 内存系统组件

| 组件 | 描述 |
| :--- | :--- |
| **短期内存** | 使用`RAG`临时存储最近的交互和结果，使代理能够在当前执行期间回忆并利用与其当前上下文相关的信息。 |
| **长期内存** | 保留过去执行中有价值的见解和学习，允许代理随着时间的推移构建和完善其知识。 |
| **实体内存** | 捕获并组织在任务过程中遇到的实体（人、地点、概念）信息，促进更深层次的理解和关系映射。使用`RAG`存储实体信息。 |
| **上下文内存** | 通过结合`ShortTermMemory`、`LongTermMemory`、`ExternalMemory`和`EntityMemory`来维护交互的上下文，有助于在一系列任务或对话中保持代理响应的连贯性和相关性。 |

## 1. 基本内存系统（推荐）

最简单且最常用的方法。只需一个参数即可为您的团队启用内存：

### 快速开始

```python  theme={null}
from crewai import Crew, Agent, Task, Process

# 启用基本内存系统
crew = Crew(
    agents=[...],
    tasks=[...],
    process=Process.sequential,
    memory=True,  # 启用短期、长期和实体内存
    verbose=True
)
```

### 工作原理

* **短期内存**：使用带有RAG的ChromaDB处理当前上下文
* **长期内存**：使用SQLite3存储跨会话的任务结果
* **实体内存**：使用RAG跟踪实体（人、地点、概念）
* **存储位置**：通过`appdirs`包确定平台特定位置
* **自定义存储目录**：设置`CREWAI_STORAGE_DIR`环境变量

## 存储位置透明度

<Info>
  **了解存储位置**：CrewAI使用遵循操作系统惯例的平台特定目录来存储内存和知识文件。了解这些位置有助于生产部署、备份和调试。
</Info>

### CrewAI存储文件的位置

默认情况下，CrewAI使用`appdirs`库根据平台惯例确定存储位置。以下是您的文件确切存储位置：

#### 各平台的默认存储位置

**macOS:**

```
~/Library/Application Support/CrewAI/{project_name}/
├── knowledge/           # 知识库ChromaDB文件
├── short_term_memory/   # 短期内存ChromaDB文件
├── long_term_memory/    # 长期内存ChromaDB文件
├── entities/            # 实体内存ChromaDB文件
└── long_term_memory_storage.db  # SQLite数据库
```

**Linux:**

```
~/.local/share/CrewAI/{project_name}/
├── knowledge/
├── short_term_memory/
├── long_term_memory/
├── entities/
└── long_term_memory_storage.db
```

**Windows:**

```
C:\Users\{username}\AppData\Local\CrewAI\{project_name}\
├── knowledge\
├── short_term_memory\
├── long_term_memory\
├── entities\
└── long_term_memory_storage.db
```

### 查找您的存储位置

要准确查看CrewAI在您的系统上存储文件的位置：

```python  theme={null}
from crewai.utilities.paths import db_storage_path
import os

# 获取基础存储路径
storage_path = db_storage_path()
print(f"CrewAI存储位置: {storage_path}")

# 列出所有CrewAI存储目录
if os.path.exists(storage_path):
    print("\n存储的文件和目录:")
    for item in os.listdir(storage_path):
        item_path = os.path.join(storage_path, item)
        if os.path.isdir(item_path):
            print(f"📁 {item}/")
            # 显示ChromaDB集合
            if os.path.exists(item_path):
                for subitem in os.listdir(item_path):
                    print(f"   └── {subitem}")
        else:
            print(f"📄 {item}")
else:
    print("尚未找到CrewAI存储目录。")
```

### 控制存储位置

#### 选项1：环境变量（推荐）

```python  theme={null}
import os
from crewai import Crew

# 设置自定义存储位置
os.environ["CREWAI_STORAGE_DIR"] = "./my_project_storage"

# 所有内存和知识现在将存储在./my_project_storage/中
crew = Crew(
    agents=[...],
    tasks=[...],
    memory=True
)
```

#### 选项2：自定义存储路径

```python  theme={null}
import os
from crewai import Crew
from crewai.memory import LongTermMemory
from crewai.memory.storage.ltm_sqlite_storage import LTMSQLiteStorage

# 配置自定义存储位置
custom_storage_path = "./storage"
os.makedirs(custom_storage_path, exist_ok=True)

crew = Crew(
    memory=True,
    long_term_memory=LongTermMemory(
        storage=LTMSQLiteStorage(
            db_path=f"{custom_storage_path}/memory.db"
        )
    )
)
```

#### 选项3：项目特定存储

```python  theme={null}
import os
from pathlib import Path

# 存储在项目目录中
project_root = Path(__file__).parent
storage_dir = project_root / "crewai_storage"

os.environ["CREWAI_STORAGE_DIR"] = str(storage_dir)

# 现在所有存储都将在您的项目目录中
```

### 嵌入提供者默认值

<Info>
  **默认嵌入提供者**：CrewAI默认使用OpenAI嵌入以确保一致性和可靠性。您可以轻松自定义此设置以匹配您的LLM提供者或使用本地嵌入。
</Info>

#### 理解默认行为

```python  theme={null}
# 当使用Claude作为您的LLM时...
from crewai import Agent, LLM

agent = Agent(
    role="Analyst",
    goal="Analyze data",
    backstory="Expert analyst",
    llm=LLM(provider="anthropic", model="claude-3-sonnet")  # 使用Claude
)

# CrewAI默认将使用OpenAI嵌入以确保一致性
# 您可以轻松自定义此设置以匹配您偏好的提供者
```

#### 自定义嵌入提供者

```python  theme={null}
from crewai import Crew

# 选项1：匹配您的LLM提供者
crew = Crew(
    agents=[agent],
    tasks=[task],
    memory=True,
    embedder={
        "provider": "anthropic", # 匹配您的LLM提供者
        "config": {
            "api_key": "your-anthropic-key",
            "model": "text-embedding-3-small"
        }
    }
)

# 选项2：使用本地嵌入（无外部API调用）
crew = Crew(
    agents=[agent],
    tasks=[task],
    memory=True,
    embedder={
        "provider": "ollama",
        "config": {"model": "mxbai-embed-large"}
    }
)
```

### 调试存储问题

#### 检查存储权限

```python  theme={null}
import os
from crewai.utilities.paths import db_storage_path

storage_path = db_storage_path()
print(f"存储路径: {storage_path}")
print(f"路径存在: {os.path.exists(storage_path)}")
print(f"是否可写: {os.access(storage_path, os.W_OK) if os.path.exists(storage_path) else '路径不存在'}")

# 使用适当的权限创建
if not os.path.exists(storage_path):
    os.makedirs(storage_path, mode=0o755, exist_ok=True)
    print(f"已创建存储目录: {storage_path}")
```

#### 检查ChromaDB集合

```python  theme={null}
import chromadb
from crewai.utilities.paths import db_storage_path

# 连接到CrewAI的ChromaDB
storage_path = db_storage_path()
chroma_path = os.path.join(storage_path, "knowledge")

if os.path.exists(chroma_path):
    client = chromadb.PersistentClient(path=chroma_path)
    collections = client.list_collections()

    print("ChromaDB集合:")
    for collection in collections:
        print(f"  - {collection.name}: {collection.count()} 个文档")
else:
    print("未找到ChromaDB存储")
```

#### 重置存储（调试）

```python  theme={null}
from crewai import Crew

# 重置所有内存存储
crew = Crew(agents=[...], tasks=[...], memory=True)

# 重置特定内存类型
crew.reset_memories(command_type='short')     # 短期内存
crew.reset_memories(command_type='long')      # 长期内存
crew.reset_memories(command_type='entity')    # 实体内存
crew.reset_memories(command_type='knowledge') # 知识存储
```

### 生产最佳实践

1. **设置`CREWAI_STORAGE_DIR`**到生产环境中的已知位置以便更好地控制
2. **选择明确的嵌入提供者**以匹配您的LLM设置
3. **监控存储目录大小**以进行大规模部署
4. **将存储目录包含**在您的备份策略中
5. **设置适当的文件权限**（目录使用0o755，文件使用0o644）
6. **使用项目相对路径**进行容器化部署

### 常见存储问题

**"ChromaDB权限被拒绝"错误:**

```bash  theme={null}
# 修复权限
chmod -R 755 ~/.local/share/CrewAI/
```

**"数据库被锁定"错误:**

```python  theme={null}
# 确保只有一个CrewAI实例访问存储
import fcntl
import os

storage_path = db_storage_path()
lock_file = os.path.join(storage_path, ".crewai.lock")

with open(lock_file, 'w') as f:
    fcntl.flock(f.fileno(), fcntl.LOCK_EX | fcntl.LOCK_NB)
    # 您的CrewAI代码在这里
```

**运行之间存储未持久化:**

```python  theme={null}
# 验证存储位置是否一致
import os
print("CREWAI_STORAGE_DIR:", os.getenv("CREWAI_STORAGE_DIR"))
print("当前工作目录:", os.getcwd())
print("计算的存储路径:", db_storage_path())
```

## 自定义嵌入器配置

CrewAI支持多个嵌入提供者，让您可以灵活选择最适合您用例的选项。以下是配置内存系统不同嵌入提供者的综合指南。

### 为什么选择不同的嵌入提供者？

* **成本优化**：本地嵌入（Ollama）在初始设置后免费
* **隐私**：使用Ollama保持数据本地化或使用您偏好的云提供者
* **性能**：某些模型在特定领域或语言上表现更好
* **一致性**：使您的嵌入提供者与LLM提供者相匹配
* **合规性**：满足特定的法规或组织要求

### OpenAI嵌入（默认）

OpenAI提供可靠、高质量的嵌入，适用于大多数用例。

```python  theme={null}
from crewai import Crew

# 基本OpenAI配置（使用环境OPENAI_API_KEY）
crew = Crew(
    agents=[...],
    tasks=[...],
    memory=True,
    embedder={
        "provider": "openai",
        "config": {
            "model": "text-embedding-3-small"  # 或 "text-embedding-3-large"
        }
    }
)

# 高级OpenAI配置
crew = Crew(
    memory=True,
    embedder={
        "provider": "openai",
        "config": {
            "api_key": "your-openai-api-key",  # 可选：覆盖环境变量
            "model": "text-embedding-3-large",
            "dimensions": 1536,  # 可选：减少维度以节省存储空间
            "organization_id": "your-org-id"  # 可选：用于组织账户
        }
    }
)
```

### Azure OpenAI嵌入

对于具有Azure OpenAI部署的企业用户。

```python  theme={null}
crew = Crew(
    memory=True,
    embedder={
        "provider": "openai",  # 为Azure使用openai提供者
        "config": {
            "api_key": "your-azure-api-key",
            "api_base": "https://your-resource.openai.azure.com/",
            "api_type": "azure",
            "api_version": "2023-05-15",
            "model": "text-embedding-3-small",
            "deployment_id": "your-deployment-name"  # Azure部署名称
        }
    }
)
```

### Google AI嵌入

使用Google的文本嵌入模型与Google Cloud服务集成。

```python  theme={null}
crew = Crew(
    memory=True,
    embedder={
        "provider": "google",
        "config": {
            "api_key": "your-google-api-key",
            "model": "text-embedding-004"  # 或 "text-embedding-preview-0409"
        }
    }
)
```

### Vertex AI嵌入

对于具有Vertex AI访问权限的Google Cloud用户。

```python  theme={null}
crew = Crew(
    memory=True,
    embedder={
        "provider": "vertexai",
        "config": {
            "project_id": "your-gcp-project-id",
            "region": "us-central1",  # 或您偏好的区域
            "api_key": "your-service-account-key",
            "model_name": "textembedding-gecko"
        }
    }
)
```

### Ollama嵌入（本地）

本地运行嵌入以保护隐私和节省成本。

```python  theme={null}
# 首先，在本地安装并运行Ollama，然后拉取一个嵌入模型：
# ollama pull mxbai-embed-large

crew = Crew(
    memory=True,
    embedder={
        "provider": "ollama",
        "config": {
            "model": "mxbai-embed-large",  # 或 "nomic-embed-text"
            "url": "http://localhost:11434/api/embeddings"  # 默认Ollama URL
        }
    }
)

# 对于自定义Ollama安装
crew = Crew(
    memory=True,
    embedder={
        "provider": "ollama",
        "config": {
            "model": "mxbai-embed-large",
            "url": "http://your-ollama-server:11434/api/embeddings"
        }
    }
)
```

### Cohere嵌入

使用Cohere的嵌入模型进行多语言支持。

```python  theme={null}
crew = Crew(
    memory=True,
    embedder={
        "provider": "cohere",
        "config": {
            "api_key": "your-cohere-api-key",
            "model": "embed-english-v3.0"  # 或 "embed-multilingual-v3.0"
        }
    }
)
```

### VoyageAI嵌入

针对检索任务优化的高性能嵌入。

```python  theme={null}
crew = Crew(
    memory=True,
    embedder={
        "provider": "voyageai",
        "config": {
            "api_key": "your-voyage-api-key",
            "model": "voyage-large-2",  # 或 "voyage-code-2" 用于代码
            "input_type": "document"  # 或 "query"
        }
    }
)
```

### AWS Bedrock嵌入

对于具有Bedrock访问权限的AWS用户。

```python  theme={null}
crew = Crew(
    memory=True,
    embedder={
        "provider": "bedrock",
        "config": {
            "aws_access_key_id": "your-access-key",
            "aws_secret_access_key": "your-secret-key",
            "region_name": "us-east-1",
            "model": "amazon.titan-embed-text-v1"
        }
    }
)
```

### Hugging Face嵌入

使用来自Hugging Face的开源模型。

```python  theme={null}
crew = Crew(
    memory=True,
    embedder={
        "provider": "huggingface",
        "config": {
            "api_key": "your-hf-token",  # 公共模型可选
            "model": "sentence-transformers/all-MiniLM-L6-v2",
            "api_url": "https://api-inference.huggingface.co"  # 或您的自定义端点
        }
    }
)
```

### IBM Watson嵌入

对于IBM Cloud用户。

```python  theme={null}
crew = Crew(
    memory=True,
    embedder={
        "provider": "watson",
        "config": {
            "api_key": "your-watson-api-key",
            "url": "your-watson-instance-url",
            "model": "ibm/slate-125m-english-rtrvr"
        }
    }
)
```

### Mem0提供者

短期内存和实体内存都支持与Mem0 OSS和Mem0 Client作为提供者的紧密集成。以下是如何使用Mem0作为提供者。

```python  theme={null}
from crewai.memory.short_term.short_term_memory import ShortTermMemory
from crewai.memory.entity_entity_memory import EntityMemory

mem0_oss_embedder_config = {
        "provider": "mem0",
        "config": {
            "user_id": "john",
            "local_mem0_config": {
                "vector_store": {"provider": "qdrant","config": {"host": "localhost", "port": 6333}},
                "llm": {"provider": "openai","config": {"api_key": "your-api-key", "model": "gpt-4"}},
                "embedder": {"provider": "openai","config": {"api_key": "your-api-key", "model": "text-embedding-3-small"}}
            },
            "infer": True # 可选，默认为True
        },
    }


mem0_client_embedder_config = {
        "provider": "mem0",
        "config": {
            "user_id": "john",
            "org_id": "my_org_id",        # 可选
            "project_id": "my_project_id", # 可选
            "api_key": "custom-api-key"    # 可选 - 覆盖环境变量
            "run_id": "my_run_id",        # 可选 - 用于短期内存
            "includes": "include1",       # 可选 
            "excludes": "exclude1",       # 可选
            "infer": True                 # 可选，默认为True
            "custom_categories": new_categories  # 可选 - 用户内存的自定义类别
        },
    }


short_term_memory_mem0_oss = ShortTermMemory(embedder_config=mem0_oss_embedder_config) # 使用Mem0 OSS的短期内存
short_term_memory_mem0_client = ShortTermMemory(embedder_config=mem0_client_embedder_config) # 使用Mem0 Client的短期内存
entity_memory_mem0_oss = EntityMemory(embedder_config=mem0_oss_embedder_config) # 使用Mem0 OSS的实体内存
entity_memory_mem0_client = EntityMemory(embedder_config=mem0_client_embedder_config) # 使用Mem0 Client的短期内存

crew = Crew(
    memory=True,
    short_term_memory=short_term_memory_mem0_oss, # 或 short_term_memory_mem0_client
    entity_memory=entity_memory_mem0_oss # 或 entity_memory_mem0_client
)
```

### 选择合适的嵌入提供者

选择嵌入提供者时，请考虑性能、隐私、成本和集成需求等因素。
以下是帮助您决定的比较：

| 提供者 | 最适合 | 优点 | 缺点 |
| --- | --- | --- | --- |
| **OpenAI** | 通用，高可靠性 | 高质量，广泛测试 | 付费服务，需要API密钥 |
| **Ollama** | 注重隐私，节省成本 | 免费，本地运行，完全私有 | 需要本地安装/设置 |
| **Google AI** | Google生态系统集成 | 强大的性能，良好的支持 | 需要Google账户 |
| **Azure OpenAI** | 企业和合规需求 | 企业级功能，安全性 | 设置过程更复杂 |
| **Cohere** | 多语言内容处理 | 优秀的语言支持 | 用例更小众 |
| **VoyageAI** | 信息检索和搜索 | 针对检索任务优化 | 相对较新的提供者 |
| **Mem0** | 按用户个性化 | 针对搜索优化的嵌入 | 付费服务，需要API密钥 |

### 环境变量配置

为了安全起见，将API密钥存储在环境变量中：

```python  theme={null}
import os

# 设置环境变量
os.environ["OPENAI_API_KEY"] = "your-openai-key"
os.environ["GOOGLE_API_KEY"] = "your-google-key"
os.environ["COHERE_API_KEY"] = "your-cohere-key"

# 在代码中使用而不暴露密钥
crew = Crew(
    memory=True,
    embedder={
        "provider": "openai",
        "config": {
            "model": "text-embedding-3-small"
            # API密钥自动从环境加载
        }
    }
)
```

### 测试不同的嵌入提供者

为您的特定用例比较嵌入提供者：

```python  theme={null}
from crewai import Crew
from crewai.utilities.paths import db_storage_path

# 使用相同数据测试不同提供者
providers_to_test = [
    {
        "name": "OpenAI",
        "config": {
            "provider": "openai",
            "config": {"model": "text-embedding-3-small"}
        }
    },
    {
        "name": "Ollama",
        "config": {
            "provider": "ollama",
            "config": {"model": "mxbai-embed-large"}
        }
    }
]

for provider in providers_to_test:
    print(f"\n测试{provider['name']}嵌入...")

    # 创建具有特定嵌入器的团队
    crew = Crew(
        agents=[...],
        tasks=[...],
        memory=True,
        embedder=provider['config']
    )

    # 运行测试并测量性能
    result = crew.kickoff()
    print(f"{provider['name']}成功完成")
```

### 嵌入问题故障排除

**模型未找到错误：**

```python  theme={null}
# 验证模型可用性
from crewai.rag.embeddings.configurator import EmbeddingConfigurator

configurator = EmbeddingConfigurator()
try:
    embedder = configurator.configure_embedder({
        "provider": "ollama",
        "config": {"model": "mxbai-embed-large"}
    })
    print("嵌入器配置成功")
except Exception as e:
    print(f"配置错误: {e}")
```

**API密钥问题：**

```python  theme={null}
import os

# 检查是否设置了API密钥
required_keys = ["OPENAI_API_KEY", "GOOGLE_API_KEY", "COHERE_API_KEY"]
for key in required_keys:
    if os.getenv(key):
        print(f"✅ {key}已设置")
    else:
        print(f"❌ {key}未设置")
```

**性能比较：**

```python  theme={null}
import time

def test_embedding_performance(embedder_config, test_text="This is a test document"):
    start_time = time.time()

    crew = Crew(
        agents=[...],
        tasks=[...],
        memory=True,
        embedder=embedder_config
    )

    # 模拟内存操作
    crew.kickoff()

    end_time = time.time()
    return end_time - start_time

# 比较性能
openai_time = test_embedding_performance({
    "provider": "openai",
    "config": {"model": "text-embedding-3-small"}
})

ollama_time = test_embedding_performance({
    "provider": "ollama",
    "config": {"model": "mxbai-embed-large"}
})

print(f"OpenAI: {openai_time:.2f}秒")
print(f"Ollama: {ollama_time:.2f}秒")
```

### 实体内存批处理行为

实体内存在一次保存多个实体时支持批处理。当您传递`EntityMemoryItem`列表时，系统：

* 发出一个带有`entity_count`的MemorySaveStartedEvent
* 在内部保存每个实体，收集任何部分错误
* 发出带有聚合元数据（保存计数，错误）的MemorySaveCompletedEvent
* 如果某些实体失败，则抛出部分保存异常（包括计数）

这在一次操作中写入许多实体时提高了性能和可观察性。

## 2. 外部内存

外部内存提供了一个独立于团队内置内存的独立内存系统。这适用于专门的内存提供者或跨应用程序内存共享。

### 使用Mem0的基本外部内存

```python  theme={null}
import os
from crewai import Agent, Crew, Process, Task
from crewai.memory.external.external_memory import ExternalMemory

# 创建具有本地Mem0配置的外部内存实例
external_memory = ExternalMemory(
    embedder_config={
        "provider": "mem0",
        "config": {
            "user_id": "john",
            "local_mem0_config": {
                "vector_store": {
                    "provider": "qdrant",
                    "config": {"host": "localhost", "port": 6333}
                },
                "llm": {
                    "provider": "openai",
                    "config": {"api_key": "your-api-key", "model": "gpt-4"}
                },
                "embedder": {
                    "provider": "openai",
                    "config": {"api_key": "your-api-key", "model": "text-embedding-3-small"}
                }
            },
            "infer": True # 可选，默认为True
        },
    }
)

crew = Crew(
    agents=[...],
    tasks=[...],
    external_memory=external_memory, # 与基本内存分离
    process=Process.sequential,
    verbose=True
)
```

### 使用Mem0 Client的高级外部内存

使用Mem0 Client时，您可以通过使用'includes'、'excludes'、'custom_categories'、'infer'和'run_id'（仅用于短期内存）等参数进一步自定义内存配置。
您可以在[Mem0文档](https://docs.mem0.ai/)中找到更多详细信息。

```python  theme={null}
import os
from crewai import Agent, Crew, Process, Task
from crewai.memory.external.external_memory import ExternalMemory

new_categories = [
    {"lifestyle_management_concerns": "跟踪日常习惯、爱好和兴趣，包括烹饪、时间管理和工作生活平衡"},
    {"seeking_structure": "记录在生活各个领域创建例程、时间表和组织系统的目标"},
    {"personal_information": "关于用户的基本信息，包括姓名、偏好和个性特征"}
]

os.environ["MEM0_API_KEY"] = "your-api-key"

# 创建具有Mem0 Client的外部内存实例
external_memory = ExternalMemory(
    embedder_config={
        "provider": "mem0",
        "config": {
            "user_id": "john",
            "org_id": "my_org_id",        # 可选
            "project_id": "my_project_id", # 可选
            "api_key": "custom-api-key"    # 可选 - 覆盖环境变量
            "run_id": "my_run_id",        # 可选 - 用于短期内存
            "includes": "include1",       # 可选 
            "excludes": "exclude1",       # 可选
            "infer": True                 # 可选，默认为True
            "custom_categories": new_categories  # 可选 - 用户内存的自定义类别
        },
    }
)

crew = Crew(
    agents=[...],
    tasks=[...],
    external_memory=external_memory, # 与基本内存分离
    process=Process.sequential,
    verbose=True
)
```

### 自定义存储实现

```python  theme={null}
from crewai.memory.external.external_memory import ExternalMemory
from crewai.memory.storage.interface import Storage

class CustomStorage(Storage):
    def __init__(self):
        self.memories = []

    def save(self, value, metadata=None, agent=None):
        self.memories.append({
            "value": value,
            "metadata": metadata,
            "agent": agent
        })

    def search(self, query, limit=10, score_threshold=0.5):
        # 在这里实现您的搜索逻辑
        return [m for m in self.memories if query.lower() in str(m["value"]).lower()]

    def reset(self):
        self.memories = []

# 使用自定义存储
external_memory = ExternalMemory(storage=CustomStorage())

crew = Crew(
    agents=[...],
    tasks=[...],
    external_memory=external_memory
)
```

## 🧠 内存系统比较

| **类别** | **功能** | **基本内存** | **外部内存** |
| --- | --- | --- | --- |
| **易用性** | 设置复杂度 | 简单 | 中等 |
| | 集成 | 内置（上下文相关） | 独立 |
| **持久性** | 存储 | 本地文件 | 自定义 / Mem0 |
| | 跨会话支持 | ✅ | ✅ |
| **个性化** | 用户特定内存 | ❌ | ✅ |
| | 自定义提供者 | 有限 | 任何提供者 |
| **用例适配** | 推荐用于 | 大多数通用用例 | 专门 / 自定义需求 |

## 支持的嵌入提供者

### OpenAI（默认）

```python  theme={null}
crew = Crew(
    memory=True,
    embedder={
        "provider": "openai",
        "config": {"model": "text-embedding-3-small"}
    }
)
```

### Ollama

```python  theme={null}
crew = Crew(
    memory=True,
    embedder={
        "provider": "ollama",
        "config": {"model": "mxbai-embed-large"}
    }
)
```

### Google AI

```python  theme={null}
crew = Crew(
    memory=True,
    embedder={
        "provider": "google",
        "config": {
            "api_key": "your-api-key",
            "model": "text-embedding-004"
        }
    }
)
```

### Azure OpenAI

```python  theme={null}
crew = Crew(
    memory=True,
    embedder={
        "provider": "openai",
        "config": {
            "api_key": "your-api-key",
            "api_base": "https://your-resource.openai.azure.com/",
            "api_version": "2023-05-15",
            "model_name": "text-embedding-3-small"
        }
    }
)
```

### Vertex AI

```python  theme={null}
crew = Crew(
    memory=True,
    embedder={
        "provider": "vertexai",
        "config": {
            "project_id": "your-project-id",
            "region": "your-region",
            "api_key": "your-api-key",
            "model_name": "textembedding-gecko"
        }
    }
)
```

## 安全最佳实践

### 环境变量

```python  theme={null}
import os
from crewai import Crew

# 将敏感数据存储在环境变量中
crew = Crew(
    memory=True,
    embedder={
        "provider": "openai",
        "config": {
            "api_key": os.getenv("OPENAI_API_KEY"),
            "model": "text-embedding-3-small"
        }
    }
)
```

### 存储安全

```python  theme={null}
import os
from crewai import Crew
from crewai.memory import LongTermMemory
from crewai.memory.storage.ltm_sqlite_storage import LTMSQLiteStorage

# 使用安全的存储路径
storage_path = os.getenv("CREWAI_STORAGE_DIR", "./storage")
os.makedirs(storage_path, mode=0o700, exist_ok=True)  # 限制权限

crew = Crew(
    memory=True,
    long_term_memory=LongTermMemory(
        storage=LTMSQLiteStorage(
            db_path=f"{storage_path}/memory.db"
        )
    )
)
```

## 故障排除

### 常见问题

**内存在会话之间没有持久化？**

* 检查`CREWAI_STORAGE_DIR`环境变量
* 确保对存储目录有写权限
* 验证内存已启用`memory=True`

**Mem0身份验证错误？**

* 验证`MEM0_API_KEY`环境变量已设置
* 在Mem0仪表板上检查API密钥权限
* 确保已安装`mem0ai`包

**大型数据集的高内存使用量？**

* 考虑使用带有自定义存储的外部内存
* 在自定义存储搜索方法中实现分页
* 使用较小的嵌入模型以减少内存占用

### 性能技巧

* 对大多数用例使用`memory=True`（最简单和最快）
* 仅在需要用户特定持久化时使用用户内存
* 对于大规模或专门需求，考虑外部内存
* 选择较小的嵌入模型以加快处理速度
* 设置适当的搜索限制以控制内存检索大小

## 使用CrewAI内存系统的好处

* 🦾 **自适应学习**：团队随着时间的推移变得更加高效，适应新信息并完善其任务处理方法。
* 🫡 **增强个性化**：内存使代理能够记住用户偏好和历史交互，从而提供个性化体验。
* 🧠 **改进问题解决**：访问丰富的内存存储有助于代理做出更明智的决策，利用过去的学习和上下文见解。

## 内存事件

CrewAI的事件系统提供了对内存操作的强大洞察力。通过利用内存事件，您可以监控、调试和优化内存系统的性能和行为。

### 可用的内存事件

CrewAI发出以下与内存相关的事件：

| 事件 | 描述 | 关键属性 |
| :--- | :--- | :--- |
| **MemoryQueryStartedEvent** | 内存查询开始时发出 | `query`, `limit`, `score_threshold` |
| **MemoryQueryCompletedEvent** | 内存查询成功完成时发出 | `query`, `results`, `limit`, `score_threshold`, `query_time_ms` |
| **MemoryQueryFailedEvent** | 内存查询失败时发出 | `query`, `limit`, `score_threshold`, `error` |
| **MemorySaveStartedEvent** | 内存保存操作开始时发出 | `value`, `metadata`, `agent_role` |
| **MemorySaveCompletedEvent** | 内存保存操作成功完成时发出 | `value`, `metadata`, `agent_role`, `save_time_ms` |
| **MemorySaveFailedEvent** | 内存保存操作失败时发出 | `value`, `metadata`, `agent_role`, `error` |
| **MemoryRetrievalStartedEvent** | 任务提示的内存检索开始时发出 | `task_id` |
| **MemoryRetrievalCompletedEvent** | 内存检索成功完成时发出 | `task_id`, `memory_content`, `retrieval_time_ms` |

### 实际应用

#### 1. 内存性能监控

跟踪内存操作时间以优化您的应用程序：

```python  theme={null}
from crewai.events import (
    BaseEventListener,
    MemoryQueryCompletedEvent,
    MemorySaveCompletedEvent
)
import time

class MemoryPerformanceMonitor(BaseEventListener):
    def __init__(self):
        super().__init__()
        self.query_times = []
        self.save_times = []

    def setup_listeners(self, crewai_event_bus):
        @crewai_event_bus.on(MemoryQueryCompletedEvent)
        def on_memory_query_completed(source, event: MemoryQueryCompletedEvent):
            self.query_times.append(event.query_time_ms)
            print(f"内存查询在{event.query_time_ms:.2f}毫秒内完成。查询: '{event.query}'")
            print(f"平均查询时间: {sum(self.query_times)/len(self.query_times):.2f}毫秒")

        @crewai_event_bus.on(MemorySaveCompletedEvent)
        def on_memory_save_completed(source, event: MemorySaveCompletedEvent):
            self.save_times.append(event.save_time_ms)
            print(f"内存保存在{event.save_time_ms:.2f}毫秒内完成")
            print(f"平均保存时间: {sum(self.save_times)/len(self.save_times):.2f}毫秒")

# 创建您的监听器实例
memory_monitor = MemoryPerformanceMonitor()
```

#### 2. 内存内容日志记录

记录内存操作以进行调试和洞察：

```python  theme={null}
from crewai.events import (
    BaseEventListener,
    MemorySaveStartedEvent,
    MemoryQueryStartedEvent,
    MemoryRetrievalCompletedEvent
)
import logging

# 配置日志记录
logger = logging.getLogger('memory_events')

class MemoryLogger(BaseEventListener):
    def setup_listeners(self, crewai_event_bus):
        @crewai_event_bus.on(MemorySaveStartedEvent)
        def on_memory_save_started(source, event: MemorySaveStartedEvent):
            if event.agent_role:
                logger.info(f"代理 '{event.agent_role}' 正在保存内存: {event.value[:50]}...")
            else:
                logger.info(f"正在保存内存: {event.value[:50]}...")

        @crewai_event_bus.on(MemoryQueryStartedEvent)
        def on_memory_query_started(source, event: MemoryQueryStartedEvent):
            logger.info(f"内存查询开始: '{event.query}' (限制: {event.limit})")

        @crewai_event_bus.on(MemoryRetrievalCompletedEvent)
        def on_memory_retrieval_completed(source, event: MemoryRetrievalCompletedEvent):
            if event.task_id:
                logger.info(f"为任务{event.task_id}检索的内存在{event.retrieval_time_ms:.2f}毫秒内完成")
            else:
                logger.info(f"内存检索在{event.retrieval_time_ms:.2f}毫秒内完成")
            logger.debug(f"内存内容: {event.memory_content}")

# 创建您的监听器实例
memory_logger = MemoryLogger()
```

#### 3. 错误跟踪和通知

捕获并响应内存错误：

```python  theme={null}
from crewai.events import (
    BaseEventListener,
    MemorySaveFailedEvent,
    MemoryQueryFailedEvent
)
import logging
from typing import Optional

# 配置日志记录
logger = logging.getLogger('memory_errors')

class MemoryErrorTracker(BaseEventListener):
    def __init__(self, notify_email: Optional[str] = None):
        super().__init__()
        self.notify_email = notify_email
        self.error_count = 0

    def setup_listeners(self, crewai_event_bus):
        @crewai_event_bus.on(MemorySaveFailedEvent)
        def on_memory_save_failed(source, event: MemorySaveFailedEvent):
            self.error_count += 1
            agent_info = f"代理 '{event.agent_role}'" if event.agent_role else "未知代理"
            error_message = f"内存保存失败: {event.error}。{agent_info}"
            logger.error(error_message)

            if self.notify_email and self.error_count % 5 == 0:
                self._send_notification(error_message)

        @crewai_event_bus.on(MemoryQueryFailedEvent)
        def on_memory_query_failed(source, event: MemoryQueryFailedEvent):
            self.error_count += 1
            error_message = f"内存查询失败: {event.error}。查询: '{event.query}'"
            logger.error(error_message)

            if self.notify_email and self.error_count % 5 == 0:
                self._send_notification(error_message)

    def _send_notification(self, message):
        # 实现您的通知系统（电子邮件、Slack等）
        print(f"[通知] 将发送到{self.notify_email}: {message}")

# 创建您的监听器实例
error_tracker = MemoryErrorTracker(notify_email="admin@example.com")
```

### 与分析平台集成

内存事件可以转发到分析和监控平台，以跟踪性能指标、检测异常和可视化内存使用模式：

```python  theme={null}
from crewai.events import (
    BaseEventListener,
    MemoryQueryCompletedEvent,
    MemorySaveCompletedEvent
)

class MemoryAnalyticsForwarder(BaseEventListener):
    def __init__(self, analytics_client):
        super().__init__()
        self.client = analytics_client

    def setup_listeners(self, crewai_event_bus):
        @crewai_event_bus.on(MemoryQueryCompletedEvent)
        def on_memory_query_completed(source, event: MemoryQueryCompletedEvent):
            # 将查询指标转发到分析平台
            self.client.track_metric({
                "event_type": "memory_query",
                "query": event.query,
                "duration_ms": event.query_time_ms,
                "result_count": len(event.results) if hasattr(event.results, "__len__") else 0,
                "timestamp": event.timestamp
            })

        @crewai_event_bus.on(MemorySaveCompletedEvent)
        def on_memory_save_completed(source, event: MemorySaveCompletedEvent):
            # 将保存指标转发到分析平台
            self.client.track_metric({
                "event_type": "memory_save",
                "agent_role": event.agent_role,
                "duration_ms": event.save_time_ms,
                "timestamp": event.timestamp
            })
```

### 内存事件监听器的最佳实践

1. **保持处理器轻量级**：避免在事件处理器中进行复杂处理，以防性能影响
2. **使用适当的日志级别**：正常操作使用INFO，详细信息使用DEBUG，问题使用ERROR
3. **尽可能批量处理指标**：在发送到外部系统之前累积指标
4. **优雅地处理异常**：确保您的事件处理器不会因意外数据而崩溃
5. **考虑内存消耗**：注意存储大量事件数据

## 结论

将CrewAI的内存系统集成到您的项目中非常简单。通过利用提供的内存组件和配置，
您可以快速为您的代理提供记忆、推理和从交互中学习的能力，从而解锁新的智能和能力水平。