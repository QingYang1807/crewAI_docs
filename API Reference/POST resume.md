# POST /resume

> 结合人工反馈恢复团队执行

## OpenAPI

````yaml enterprise-api.en.yaml post /resume
paths:
  path: /resume
  method: post
  servers:
    - url: https://your-actual-crew-name.crewai.com
      description: 替换为 CrewAI AMP 仪表板中的实际部署团队 URL
    - url: https://my-travel-crew.crewai.com
      description: 示例旅行规划团队（请替换为您的 URL）
    - url: https://content-creation-crew.crewai.com
      description: 示例内容创建团队（请替换为您的 URL）
    - url: https://research-assistant-crew.crewai.com
      description: 示例研究助理团队（请替换为您的 URL）
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
                **📋 参考文档** - *示例中显示的令牌仅作参考用途。*


                请使用 CrewAI AMP 仪表板中的实际 Bearer 令牌或用户 Bearer 令牌进行真实的 API 调用。


                **Bearer 令牌**：组织级别的访问权限，用于完整的团队操作


                **用户 Bearer 令牌**：用户范围访问，具有有限权限
          cookie: {}
    parameters:
      path: {}
      query: {}
      header: {}
      cookie: {}
    body:
      application/json:
        schemaArray:
          - type: object
            properties:
              execution_id:
                allOf:
                  - type: string
                    format: uuid
                    description: >-
                      团队执行的唯一标识符（来自启动）
                    example: abcd1234-5678-90ef-ghij-klmnopqrstuv
              task_id:
                allOf:
                  - type: string
                    description: 需要人工反馈的任务 ID
                    example: research_task
              human_feedback:
                allOf:
                  - type: string
                    description: >-
                      您对任务输出的反馈。这将作为后续任务执行的额外上下文。
                    example: >-
                      研究很棒！请添加更多关于该领域最新发展的细节。
              is_approve:
                allOf:
                  - type: boolean
                    description: >-
                      是否批准任务输出：true = 正面反馈（继续），false = 负面反馈（重试任务）
                    example: true
              taskWebhookUrl:
                allOf:
                  - type: string
                    format: uri
                    description: >-
                      每个任务完成后执行的回调 URL。必须提供此项以继续接收任务通知。
                    example: https://your-server.com/webhooks/task
              stepWebhookUrl:
                allOf:
                  - type: string
                    format: uri
                    description: >-
                      每个代理思考/操作后执行的回调 URL。必须提供此项以继续接收步骤通知。
                    example: https://your-server.com/webhooks/step
              crewWebhookUrl:
                allOf:
                  - type: string
                    format: uri
                    description: >-
                      团队执行完成时执行的回调 URL。必须提供此项以接收完成通知。
                    example: https://your-server.com/webhooks/crew
            required: true
            requiredProperties:
              - execution_id
              - task_id
              - human_feedback
              - is_approve
        examples:
          approve_and_continue:
            summary: 批准任务并继续执行
            value:
              execution_id: abcd1234-5678-90ef-ghij-klmnopqrstuv
              task_id: research_task
              human_feedback: 研究非常出色！请继续下一个任务。
              is_approve: true
              taskWebhookUrl: https://api.example.com/webhooks/task
              stepWebhookUrl: https://api.example.com/webhooks/step
              crewWebhookUrl: https://api.example.com/webhooks/crew
          request_revision:
            summary: 要求任务修改并提供反馈
            value:
              execution_id: abcd1234-5678-90ef-ghij-klmnopqrstuv
              task_id: analysis_task
              human_feedback: 请包含更多定量数据并引用您的来源。
              is_approve: false
              taskWebhookUrl: https://api.example.com/webhooks/task
              crewWebhookUrl: https://api.example.com/webhooks/crew
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
                      - resumed
                      - retrying
                      - completed
                    description: 恢复执行的状态
                    example: resumed
              message:
                allOf:
                  - type: string
                    description: 关于恢复操作的可读消息
                    example: 执行成功恢复
        examples:
          resumed:
            summary: 带有正面反馈的执行已恢复
            value:
              status: resumed
              message: 执行成功恢复
          retrying:
            summary: 任务将因负面反馈而重试
            value:
              status: retrying
              message: 任务将根据您的反馈重试
        description: 执行成功恢复
    '400':
      application/json:
        schemaArray:
          - type: object
            properties:
              error:
                allOf:
                  - &ref_0
                    type: string
                    description: 错误类型或标题
                    example: 认证错误
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
              error: 无效请求
              message: 执行未处于待处理人工输入状态
        description: 请求体无效或执行未处于待处理状态
    '401':
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
              error: 未授权
              message: bearer 令牌无效或缺失
        description: 认证失败 - 请检查您的 bearer 令牌
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
              error: 未找到
              message: 未找到执行 ID
        description: 未找到执行 ID 或任务 ID
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
  schemas: {}

````