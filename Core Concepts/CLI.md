# CLI

> 了解如何使用 CrewAI CLI 与 CrewAI 进行交互。

<Warning>自 0.140.0 版本发布以来，CrewAI AMP 开始迁移其登录提供商。因此，通过 CLI 的认证流程已更新。使用 Google 登录或在 2025 年 7 月 3 日之后创建账户的用户将无法使用旧版本的 `crewai` 库进行登录。</Warning>

## 概述

CrewAI CLI 提供了一组命令来与 CrewAI 交互，允许您创建、训练、运行和管理Crew（团队）及流程。

## 安装

要使用 CrewAI CLI，请确保已安装 CrewAI：

```shell
pip install crewai
```

## 基本用法

CrewAI CLI 命令的基本结构是：

```shell
crewai [命令] [选项] [参数]
```

## 可用命令

### 1. 创建

创建新的Crew（团队）或流程。

```shell
crewai create [选项] 类型 名称
```

* `类型`：在 "crew"（Crew（团队））或 "flow"（流程）之间选择
* `名称`：Crew（团队）或流程的名称

示例：

```shell
crewai create crew my_new_crew
crewai create flow my_new_flow
```

### 2. 版本

显示 CrewAI 的安装版本。

```shell
crewai version [选项]
```

* `--tools`：（可选）显示 CrewAI 工具的安装版本

示例：

```shell
crewai version
crewai version --tools
```

### 3. 训练

训练指定迭代次数的Crew（团队）。

```shell
crewai train [选项]
```

* `-n, --n_iterations 整数`：训练Crew（团队）的迭代次数（默认：5）
* `-f, --filename 文本`：用于训练的自定义文件路径（默认："trained_agents_data.pkl"）

示例：

```shell
crewai train -n 10 -f my_training_data.pkl
```

### 4. 重放

从特定任务重放Crew（团队）执行。

```shell
crewai replay [选项]
```

* `-t, --task_id 文本`：从此任务 ID 重放Crew（团队）执行，包括所有后续任务

示例：

```shell
crewai replay -t task_123456
```

### 5. 记录任务输出

检索您最新的 crew.kickoff() 任务输出。

```shell
crewai log-tasks-outputs
```

### 6. 重置记忆

重置Crew（团队）记忆（长期、短期、实体、最新Crew（团队）启动输出）。

```shell
crewai reset-memories [选项]
```

* `-l, --long`：重置长期记忆
* `-s, --short`：重置短期记忆
* `-e, --entities`：重置实体记忆
* `-k, --kickoff-outputs`：重置最新启动任务输出
* `-kn, --knowledge`：重置知识存储
* `-akn, --agent-knowledge`：重置Agent知识存储
* `-a, --all`：重置所有记忆

示例：

```shell
crewai reset-memories --long --short
crewai reset-memories --all
```

### 7. 测试

测试Crew（团队）并评估结果。

```shell
crewai test [选项]
```

* `-n, --n_iterations 整数`：测试Crew（团队）的迭代次数（默认：3）
* `-m, --model 文本`：在Crew（团队）上运行测试的 LLM 模型（默认："gpt-4o-mini"）

示例：

```shell
crewai test -n 5 -m gpt-3.5-turbo
```

### 8. 运行

运行Crew（团队）或流程。

```shell
crewai run
```

<Note>
  从 0.103.0 版本开始，`crewai run` 命令可用于运行标准Crew（团队）和流程。对于流程，它会自动从 pyproject.toml 中检测类型并运行相应的命令。这是现在运行Crew（团队）和流程的推荐方式。
</Note>

<Note>
  确保从设置 CrewAI 项目的目录中运行这些命令。
  某些命令可能需要在项目结构中进行额外的配置或设置。
</Note>

### 9. 聊天

从 `0.98.0` 版本开始，当您运行 `crewai chat` 命令时，您将启动与Crew（团队）的交互式会话。AI 助手将通过请求执行Crew（团队）所需的必要输入来指导您。一旦提供所有输入，Crew（团队）将执行其任务。

收到结果后，您可以继续与助手交互以获取进一步的指示或问题。

```shell
crewai chat
```

<Note>
  确保从您的 CrewAI 项目根目录执行这些命令。
</Note>

<Note>
  重要提示：在您的 `crew.py` 文件中设置 `chat_llm` 属性以启用此命令。

  ```python  theme={null}
  @crew
  def crew(self) -> Crew:
      return Crew(
          agents=self.agents,
          tasks=self.tasks,
          process=Process.sequential,
          verbose=True,
          chat_llm="gpt-4o",  # 用于聊天编排的 LLM
      )
  ```
</Note>

### 10. 部署

将Crew（团队）或流程部署到 [CrewAI AMP](https://app.crewai.com)。

* **认证**：您需要经过身份验证才能部署到 CrewAI AMP。
  您可以使用以下命令登录或创建账户：
  ```shell
  crewai login
  ```

* **创建部署**：通过身份验证后，您可以从本地项目的根目录为Crew（团队）或流程创建部署。
  ```shell
  crewai deploy create
  ```
  * 读取您的本地项目配置。
  * 提示您确认在本地找到的环境变量（如 `OPENAI_API_KEY`、`SERPER_API_KEY`）。这些将与企业平台上的部署安全存储。在运行此命令之前，请确保您的敏感密钥已在本地正确配置（例如，在 `.env` 文件中）。

### 11. 组织管理

管理您的 CrewAI AMP 组织。

```shell
crewai org [命令] [选项]
```

#### 命令：

* `list`：列出您所属的所有组织

```shell
crewai org list
```

* `current`：显示您当前活动的组织

```shell
crewai org current
```

* `switch`：切换到特定组织

```shell
crewai org switch <组织ID>
```

<Note>
  使用这些组织管理命令，您必须通过 CrewAI AMP 的身份验证。
</Note>

* **创建部署**（续）：
  * 将部署链接到相应的远程 GitHub 存储库（通常自动检测）。

* **部署Crew（团队）**：通过身份验证后，您可以将Crew（团队）或流程部署到 CrewAI AMP。
  ```shell
  crewai deploy push
  ```
  * 在 CrewAI AMP 平台上启动部署过程。
  * 成功启动后，它将输出 "部署创建成功！" 消息以及部署名称和唯一的部署 ID（UUID）。

* **部署状态**：您可以使用以下命令检查部署状态：
  ```shell
  crewai deploy status
  ```
  这将获取您最近部署尝试的最新部署状态（例如，"为Crew（团队）构建镜像"、"部署已排队"、"在线"）。

* **部署日志**：您可以使用以下命令检查部署日志：
  ```shell
  crewai deploy logs
  ```
  这会将部署日志流式传输到您的终端。

* **列出部署**：您可以使用以下命令列出所有部署：
  ```shell
  crewai deploy list
  ```
  这将列出您的所有部署。

* **删除部署**：您可以使用以下命令删除部署：
  ```shell
  crewai deploy remove
  ```
  这将从 CrewAI AMP 平台删除部署。

* **帮助命令**：您可以使用以下命令获取 CLI 的帮助：
  ```shell
  crewai deploy --help
  ```
  这将显示 CrewAI 部署 CLI 的帮助消息。

观看此视频教程，了解使用 CLI 将Crew（团队）部署到 [CrewAI AMP](http://app.crewai.com) 的分步演示。

<iframe className="w-full aspect-video rounded-xl" src="https://www.youtube.com/embed/3EqSV-CYDZA" title="CrewAI Deployment Guide" frameBorder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowFullScreen />

### 11. 登录

使用安全的设备代码流程与 CrewAI AMP 进行身份验证（无需输入电子邮件）。

```shell
crewai login
```

发生什么：

* 验证 URL 和短代码显示在您的终端中
* 您的浏览器打开到验证 URL
* 输入/确认代码以完成身份验证

注意事项：

* OAuth2 提供商和域通过 `crewai config` 配置（默认使用 `login.crewai.com`）
* 成功登录后，CLI 还会尝试自动向工具存储库进行身份验证
* 如果您重置配置，请再次运行 `crewai login` 以重新进行身份验证

### 12. API 密钥

当运行 `crewai create crew` 命令时，CLI 将显示可用的 LLM 提供商列表供您选择，然后是为您选择的提供商选择模型。

选择 LLM 提供商和模型后，系统将提示您输入 API 密钥。

#### 可用的 LLM 提供商

以下是 CLI 建议的最受欢迎的 LLM 提供商列表：

* OpenAI
* Groq
* Anthropic
* Google Gemini
* SambaNova

当您选择提供商时，CLI 将显示该提供商的可用模型，并提示您输入 API 密钥。

#### 其他选项

如果您选择"其他"，您将能够从 LiteLLM 支持的提供商列表中选择。

当您选择提供商时，CLI 将提示您输入密钥名称和 API 密钥。

请参阅以下链接了解每个提供商的密钥名称：

* [LiteLLM 提供商](https://docs.litellm.ai/docs/providers)

### 13. 配置管理

管理 CrewAI 的 CLI 配置设置。

```shell
crewai config [命令] [选项]
```

#### 命令：

* `list`：显示所有 CLI 配置参数

```shell
crewai config list
```

* `set`：设置 CLI 配置参数

```shell
crewai config set <键> <值>
```

* `reset`：将所有 CLI 配置参数重置为默认值

```shell
crewai config reset
```

#### 可用的配置参数

* `enterprise_base_url`：CrewAI AMP 实例的基本 URL
* `oauth2_provider`：用于身份验证的 OAuth2 提供商（例如，workos、okta、auth0）
* `oauth2_audience`：OAuth2 受众值，通常用于标识目标 API 或资源
* `oauth2_client_id`：提供商发出的 OAuth2 客户端 ID，在身份验证请求期间使用
* `oauth2_domain`：OAuth2 提供商的域（例如，your-org.auth0.com），用于颁发令牌

#### 示例

显示当前配置：

```shell
crewai config list
```

示例输出：

| 设置                 | 值                                              | 描述                                    |
| :------------------ | :--------------------------------------------- | :------------------------------------- |
| enterprise_base_url | [https://app.crewai.com](https://app.crewai.com) | CrewAI AMP 实例的基本 URL              |
| org_name            | 未设置                                          | 当前活动组织的名称                      |
| org_uuid            | 未设置                                          | 当前活动组织的 UUID                    |
| oauth2_provider     | workos                                         | OAuth2 提供商（例如，workos、okta、auth0） |
| oauth2_audience     | client_01YYY                                   | 标识目标 API/资源的受众                |
| oauth2_client_id    | client_01XXX                                   | 提供商发出的 OAuth2 客户端 ID          |
| oauth2_domain       | login.crewai.com                               | 提供商域（例如，your-org.auth0.com）     |

设置企业基本 URL：

```shell
crewai config set enterprise_base_url https://my-enterprise.crewai.com
```

设置 OAuth2 提供商：

```shell
crewai config set oauth2_provider auth0
```

设置 OAuth2 域：

```shell
crewai config set oauth2_domain my-company.auth0.com
```

将所有配置重置为默认值：

```shell
crewai config reset
```

重置配置后，重新运行`crewai login`以再次进行身份验证。


CrewAI CLI 在向项目添加包时自动处理向工具存储库的身份验证。只需在任何 `uv` 命令前附加 `crewai` 即可使用它。例如 `crewai uv add requests`。有关更多信息，请参阅 [工具存储库](https://docs.crewai.com/enterprise/features/tool-repository) 文档。


配置设置存储在 `~/.config/crewai/settings.json` 中。某些设置（如组织名称和 UUID）是只读的，通过身份验证和组织命令进行管理。与工具存储库相关的设置是隐藏的，不能由用户直接设置。