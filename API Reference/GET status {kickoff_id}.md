# GET /status/{kickoff_id}

> 获取执行状态

## OpenAPI

````yaml enterprise-api.en.yaml get /status/{kickoff_id}
paths:
  path: /status/{kickoff_id}
  method: get
  servers:
    - url: https://your-actual-crew-name.crewai.com
      description: 请替换为从 CrewAI AMP 仪表盘获取的实际部署 crew URL
    - url: https://my-travel-crew.crewai.com
      description: 示例旅行规划 crew（请替换为您的 URL）
    - url: https://content-creation-crew.crewai.com
      description: 示例内容创建 crew（请替换为您的 URL）
    - url: https://research-assistant-crew.crewai.com
      description: 示例研究助手 crew（请替换为您的 URL）
  request:
    security:
      - title: BearerAuth
        parameters:
          query: {}
          header:
            Authorization:
              type: http
              scheme: bearer
              description: >
                **📋 参考文档** - *示例中显示的令牌仅为参考占位符。*


                请使用您从 CrewAI AMP 仪表盘获取的实际 Bearer 令牌或用户 Bearer 令牌进行真实的 API 调用。


                **Bearer 令牌**：组织级别的访问权限，用于完整的 crew 操作


                **用户 Bearer 令牌**：用户范围访问权限，权限受限
          cookie: {}
    parameters:
      path:
        kickoff_id:
          schema:
            - type: string
              required: true
              description: 从 /kickoff 端点返回的 kickoff ID
              format: uuid
              example: abcd1234-5678-90ef-ghij-klmnopqrstuv
      query: {}
      header: {}
      cookie: {}
    body: {}
  response:
    '200':
      application/json:
        schemaArray:
          - type: object
            properties:
              status:
                allOf:
                  - type: string
                    enum:
                      - running
                    example: running
              current_task:
                allOf:
                  - type: string
                    description: 当前正在执行的任务名称
                    example: research_task
              progress:
                allOf:
                  - type: object
                    properties:
                      completed_tasks:
                        type: integer
                        description: 已完成的任务数量
                        example: 1
                      total_tasks:
                        type: integer
                        description: crew 中的任务总数
                        example: 3
            refIdentifier: '#/components/schemas/ExecutionRunning'
          - type: object
            properties:
              status:
                allOf:
                  - type: string
                    enum:
                      - completed
                    example: completed
              result:
                allOf:
                  - type: object
                    properties:
                      output:
                        type: string
                        description: crew 执行的最终输出
                        example: 综合旅行行程...
                      tasks:
                        type: array
                        items:
                          $ref: '#/components/schemas/TaskResult'
              execution_time:
                allOf:
                  - type: number
                    description: 总执行时间（以秒为单位）
                    example: 108.5
            refIdentifier: '#/components/schemas/ExecutionCompleted'
          - type: object
            properties:
              status:
                allOf:
                  - type: string
                    enum:
                      - error
                    example: error
              error:
                allOf:
                  - type: string
                    description: 描述错误原因的错误消息
                    example: '任务执行失败：无效的 API 密钥'
              execution_time:
                allOf:
                  - type: number
                    description: 发生错误前的时间（以秒为单位）
                    example: 23.1
            refIdentifier: '#/components/schemas/ExecutionError'
        examples:
          running:
            summary: 执行进行中
            value:
              status: running
              current_task: research_task
              progress:
                completed_tasks: 1
                total_tasks: 3
          completed:
            summary: 执行成功完成
            value:
              status: completed
              result:
                output: >-
                  为期 7 天的日本科技文化主题综合旅行行程...
                tasks:
                  - task_id: research_task
                    output: 关于日本科技目的地的研究发现...
                    agent: 旅行研究员
                    execution_time: 45.2
                  - task_id: planning_task
                    output: >-
                      为期 7 天的详细行程，包含活动和建议...
                    agent: 行程规划师
                    execution_time: 62.8
              execution_time: 108.5
          error:
            summary: 执行失败
            value:
              status: error
              error: '任务执行失败：外部服务的 API 密钥无效'
              execution_time: 23.1
        description: 成功获取执行状态
    '401':
      application/json:
        schemaArray:
          - type: object
            properties:
              error:
                allOf:
                  - &ref_0
                    type: string
                    description: 错误类型或标题
                    example: 身份验证错误
              message:
                allOf:
                  - &ref_1
                    type: string
                    description: 详细错误消息
                    example: 提供的 bearer 令牌无效
            refIdentifier: '#/components/schemas/Error'
        examples:
          example:
            value:
              error: 未授权
              message: bearer 令牌无效或缺失
        description: 身份验证失败 - 请检查您的 bearer 令牌
    '404':
      application/json:
        schemaArray:
          - type: object
            properties:
              error:
                allOf:
                  - *ref_0
              message:
                allOf:
                  - *ref_1
            refIdentifier: '#/components/schemas/Error'
        examples:
          example:
            value:
              error: 未找到执行记录
              message: '未找到 ID 为 abcd1234-5678-90ef-ghij-klmnopqrstuv 的执行记录'
        description: 未找到 Kickoff ID
    '500':
      application/json:
        schemaArray:
          - type: object
            properties:
              error:
                allOf:
                  - *ref_0
              message:
                allOf:
                  - *ref_1
            refIdentifier: '#/components/schemas/Error'
        examples:
          example:
            value:
              error: 内部服务器错误
              message: 发生意外错误
        description: 内部服务器错误
  deprecated: false
  type: path
components:
  schemas:
    TaskResult:
      type: object
      properties:
        task_id:
          type: string
          description: 任务的唯一标识符
          example: research_task
        output:
          type: string
          description: 此任务生成的输出
          example: 研究结果...
        agent:
          type: string
          description: 执行此任务的代理名称
          example: 旅行研究员
        execution_time:
          type: number
          description: 执行此任务所用的时间（以秒为单位）
          example: 45.2

````