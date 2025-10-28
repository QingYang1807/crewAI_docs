    # 安装

> 开始使用 CrewAI - 安装、配置并构建您的第一个 AI 团队

## 视频教程

观看此视频教程，了解安装过程的分步演示：

<iframe className="w-full aspect-video rounded-xl" src="https://www.youtube.com/embed/-kSOTtYzgEw" title="CrewAI 安装指南" frameBorder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowFullScreen />

## 文字教程

<Note>
  **Python 版本要求**

  CrewAI 要求 `Python >=3.10 且 <3.14`。以下是检查您的版本的方法：

  ```bash  theme={null}
  python3 --version
  ```

  如果您需要更新 Python，请访问 [python.org/downloads](https://python.org/downloads)
</Note>

<Note>
  **OpenAI SDK 要求**

  CrewAI 0.175.0 要求 `openai >= 1.13.3`。如果您自行管理依赖项，请确保您的环境满足此约束条件，以避免导入/运行时问题。
</Note>

CrewAI 使用 `uv` 作为其依赖管理和包处理工具。它简化了项目设置和执行，提供无缝体验。

如果您尚未安装 `uv`，请按照**步骤 1**在系统上快速设置它，否则可以跳到**步骤 2**。

<Steps>
  <Step title="安装 uv">
    * **在 macOS/Linux 上：**

      使用 `curl` 下载脚本并使用 `sh` 执行：

      ```shell  theme={null}
      curl -LsSf https://astral.sh/uv/install.sh | sh
      ```

      如果您的系统没有 `curl`，可以使用 `wget`：

      ```shell  theme={null}
      wget -qO- https://astral.sh/uv/install.sh | sh
      ```

    * **在 Windows 上：**

      使用 `irm` 下载脚本并使用 `iex` 执行：

      ```shell  theme={null}
      powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
      ```

      如果遇到任何问题，请参考 [UV 的安装指南](https://docs.astral.sh/uv/getting-started/installation/) 获取更多信息。
  </Step>

  <Step title="安装 CrewAI 🚀">
    * 运行以下命令安装 `crewai` CLI：

      ```shell  theme={null}
      uv tool install crewai
      ```

      <Warning>
        如果遇到 `PATH` 警告，请运行此命令更新您的 shell：

        ```shell  theme={null}
        uv tool update-shell
        ```
      </Warning>

      <Warning>
        如果在 Windows 上遇到 `chroma-hnswlib==0.7.6` 构建错误（`fatal error C1083: Cannot open include file: 'float.h'`），请安装带有 *使用 C++ 的桌面开发* 的 [Visual Studio 构建工具](https://visualstudio.microsoft.com/downloads/)。
      </Warning>

    * 要验证 `crewai` 是否已安装，请运行：
      ```shell  theme={null}
      uv tool list
      ```

    * 您应该看到类似以下内容：
      ```shell  theme={null}
      crewai v0.102.0
      - crewai
      ```

    * 如果需要更新 `crewai`，请运行：
      ```shell  theme={null}
      uv tool install crewai --upgrade
      ```

    <Check>安装成功！您已准备好创建您的第一个团队！🎉</Check>
  </Step>
</Steps>

# 创建 CrewAI 项目

我们建议使用 `YAML` 模板脚手架，以结构化的方式定义代理和任务。以下是如何开始：

<Steps>
  <Step title="生成项目脚手架">
    * 运行 `crewai` CLI 命令：
      ```shell  theme={null}
      crewai create crew <your_project_name>
      ```

    * 这将创建一个具有以下结构的新项目：
      ```
      my_project/
      ├── .gitignore
      ├── knowledge/
      ├── pyproject.toml
      ├── README.md
      ├── .env
      └── src/
          └── my_project/
              ├── __init__.py
              ├── main.py
              ├── crew.py
              ├── tools/
              │   ├── custom_tool.py
              │   └── __init__.py
              └── config/
                  ├── agents.yaml
                  └── tasks.yaml
      ```
  </Step>

  <Step title="自定义您的项目">
    * 您的项目将包含这些基本文件：
      | 文件          | 用途                                  |
      | ------------- | ---------------------------------------- |
      | `agents.yaml` | 定义您的 AI 代理及其角色    |
      | `tasks.yaml`  | 设置代理任务和工作流程         |
      | `.env`        | 存储 API 密钥和环境变量 |
      | `main.py`     | 项目入口点和执行流程   |
      | `crew.py`     | 团队编排和协调      |
      | `tools/`      | 自定义代理工具目录         |
      | `knowledge/`  | 知识库目录             |

    * 首先编辑 `agents.yaml` 和 `tasks.yaml` 来定义您的团队行为。

    * 将 API 密钥等敏感信息保存在 `.env` 中。
  </Step>

  <Step title="运行您的团队">
    * 在运行您的团队之前，请确保运行：
      ```bash  theme={null}
      crewai install
      ```
    * 如果需要安装额外的软件包，请使用：
      ```shell  theme={null}
      uv add <package-name>
      ```
    * 要运行您的团队，请在项目根目录中执行以下命令：
      ```bash  theme={null}
      crewai run
      ```
  </Step>
</Steps>

## 企业安装选项

<Note type="info">
  对于团队和组织，CrewAI 提供企业部署选项，消除设置复杂性：

  ### CrewAI AMP (SaaS)

  * 无需安装 - 只需在 [app.crewai.com](https://app.crewai.com) 免费注册
  * 自动更新和维护
  * 托管基础设施和扩展
  * 无代码构建团队

  ### CrewAI Factory (自托管)

  * 为您的基础设施提供容器化部署
  * 支持任何超大规模云服务商，包括本地部署
  * 与您现有的安全系统集成

  <Card title="探索企业选项" icon="building" href="https://crewai.com/enterprise">
    了解 CrewAI 的企业产品并安排演示
  </Card>
</Note>

## 后续步骤

<CardGroup cols={2}>
  <Card title="构建您的第一个代理" icon="code" href="/en/quickstart">
    按照我们的快速入门指南创建您的第一个 CrewAI 代理并获得实践经验。
  </Card>

  <Card title="加入社区" icon="comments" href="https://community.crewai.com">
    与其他开发者联系，获取帮助并分享您的 CrewAI 经验。
  </Card>
</CardGroup>