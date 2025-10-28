# POST /kickoff

> 启动团队执行

## OpenAPI

````yaml enterprise-api.en.yaml post /kickoff
paths:
  path: /kickoff
  method: post
  servers:
    - url: https://your-actual-crew-name.crewai.com
      description: 请使用您从 CrewAI AMP 仪表板获取的实际部署团队 URL 替换
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
                **📋 参考文档** - *示例中显示的令牌仅作为参考占位符。*


                在实际 API 调用中，请使用您从 CrewAI AMP 仪表板获取的实际 Bearer 令牌或用户 Bearer 令牌。


                **Bearer 令牌**：组织级别访问权限，用于完整的团队操作

                **用户 Bearer 令牌**：用户范围访问权限，具有有限权限
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
              inputs:
                allOf:
                  - type: object
                    description: 团队所有必需输入的键值对
                    additionalProperties:
                      type: string
                    example:
                      budget: 1000 USD
                      interests: games, tech, ai, relaxing hikes, amazing food
                      duration: 7 days
                      age: '35'
              meta:
                allOf:
                  - type: object
                    description: 传递给团队的附加元数据
                    additionalProperties: true
                    example:
                      requestId: user-request-12345
                      source: mobile-app
              taskWebhookUrl:
                allOf:
                  - type: string
                    format: uri
                    description: 每个任务完成后执行的回调 URL
                    example: https://your-server.com/webhooks/task
              stepWebhookUrl:
                allOf:
                  - type: string
                    format: uri
                    description: 每个代理思考/动作后执行的回调 URL
                    example: https://your-server.com/webhooks/step
              crewWebhookUrl:
                allOf:
                  - type: string
                    format: uri
                    description: 团队执行完成后执行的回调 URL
                    example: https://your-server.com/webhooks/crew
            required: true
            requiredProperties:
              - inputs
        examples:
          travel_planning:
            summary: 旅行规划团队
            value:
              inputs:
                budget: 1000 USD
                interests: games, tech, ai, relaxing hikes, amazing food
                duration: 7 days
                age: '35'
              meta:
                requestId: travel-req-123
                source: web-app
          outreach_campaign:
            summary: 带有 Webhook 的外联团队
            value:
              inputs:
                name: John Smith
                title: CTO
                company: TechCorp
                industry: Software
                our_product: AI Development Platform
                linkedin_url: https://linkedin.com/in/johnsmith
              taskWebhookUrl: https://api.example.com/webhooks/task
              crewWebhookUrl: https://api.example.com/webhooks/crew
  response:
    '200':
      application/json:
        schemaArray:
          - type: object
            properties:
              kickoff_id:
                allOf:
                  - type: string
                    format: uuid
                    description: 用于跟踪此执行的唯一标识符
                    example: abcd1234-5678-90ef-ghij-klmnopqrstuv
        examples:
          example:
            value:
              kickoff_id: abcd1234-5678-90ef-ghij-klmnopqrstuv
        description: 团队执行启动成功
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
                    description: 详细错误信息
                    example: 提供的无效承载令牌
            refIdentifier: '#/components/schemas/Error'
        examples:
          example:
            value:
              error: 认证错误
              message: 提供的无效承载令牌
        description: 无效的请求正文或缺少必需的输入
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
              message: 无效或缺失的承载令牌
        description: 认证失败 - 请检查您的承载令牌
    '422':
      application/json:
        schemaArray:
          - type: object
            properties:
              error:
                allOf:
                  - type: string
                    example: 验证错误
              message:
                allOf:
                  - type: string
                    example: 缺少必需的输入
              details:
                allOf:
                  - type: object
                    properties:
                      missing_inputs:
                        type: array
                        items:
                          type: string
                        example:
                          - budget
                          - interests
            refIdentifier: '#/components/schemas/ValidationError'
        examples:
          example:
            value:
              error: 验证错误
              message: 缺少必需的输入
              details:
                missing_inputs:
                  - budget
                  - interests
        description: 验证错误 - 请确保提供了所有必需的输入
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
              message: 发生了意外错误
        description: 内部服务器错误
  deprecated: false
  type: path
components:
  schemas: {}

````