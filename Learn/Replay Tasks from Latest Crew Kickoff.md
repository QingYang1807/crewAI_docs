# 从最新Crew启动中重播任务

> 从最新的crew.kickoff(...)重播任务

## 介绍

CrewAI提供了从最新crew启动中指定任务进行重播的能力。当你完成了一次启动，可能想要重试某些任务，或者不需要重新获取数据，而且你的代理已经保存了启动执行的上下文，这种情况下，你只需要重播你想要的任务，这个功能就特别有用。

<Note>
  在重播任务之前，你必须先运行`crew.kickoff()`。
  目前只支持最新的启动，所以如果你使用了`kickoff_for_each`，它只允许你重播最近的crew运行。
</Note>

以下是如何从任务重播的示例：

### 使用CLI从特定任务重播

要使用重播功能，请按照以下步骤操作：

<Steps>
  <Step title="打开你的终端或命令提示符。" />

  <Step title="导航到你的CrewAI项目所在的目录。" />

  <Step title="运行以下命令：">
    要查看最新的启动task\_ids，使用：

    ```shell  theme={null}
    crewai log-tasks-outputs
    ```

    一旦你有了要重播的`task_id`，使用：

    ```shell  theme={null}
    crewai replay -t <task_id>
    ```
  </Step>
</Steps>

<Note>
  确保`crewai`在你的开发环境中已正确安装和配置。
</Note>

### 以编程方式从任务重播

要以编程方式从任务重播，请使用以下步骤：

<Steps>
  <Step title="为重播过程指定`task_id`和输入参数。">
    为重播过程指定`task_id`和输入参数。
  </Step>

  <Step title="在try-except块中执行重播命令，以处理潜在错误。">
    在try-except块中执行重播命令，以处理潜在错误。

    <CodeGroup>
      ```python Code theme={null}
        def replay():
        """
        从特定任务重播crew执行。
        """
        task_id = '<task_id>'
        inputs = {"topic": "CrewAI Training"}  # 这是可选的；你可以传入想要重播的输入；否则，它将使用之前启动的输入。
        try:
            YourCrewName_Crew().crew().replay(task_id=task_id, inputs=inputs)

        except subprocess.CalledProcessError as e:
            raise Exception(f"重播crew时发生错误：{e}")

        except Exception as e:
            raise Exception(f"发生意外错误：{e}")
      ```
    </CodeGroup>
  </Step>
</Steps>

## 结论

通过上述增强和详细功能，CrewAI中重播特定任务变得更加高效和健壮。
确保你精确地遵循命令和步骤，以充分利用这些功能。