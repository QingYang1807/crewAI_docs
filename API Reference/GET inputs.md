# GET /inputs

> 获取您的团队所需的输入参数

## OpenAPI

````yaml enterprise-api.en.yaml get /inputs
paths:
  path: /inputs
  method: get
  servers:
    - url: https://your-actual-crew-name.crewai.com
      description: 替换为您从 CrewAI AMP 仪表板获取的实际部署团队 URL
    - url: https://my-travel-crew.crewai.com
      description: 示例旅行规划团队（请替换为您的 URL）
    - url: https://content-creation-crew.crewai.com
      description: 示例内容创作团队（请替换为您的 URL）
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


                请使用您从 CrewAI AMP 仪表板获取的实际 Bearer 令牌或用户 Bearer 令牌进行真实的 API 调用。


                **Bearer 令牌**：组织级别的访问权限，用于完整的团队操作

                **用户 Bearer 令牌**：用户范围的访问权限，权限有限
          cookie: {}
    parameters:
      path: {}
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
              inputs:
                allOf:
                  - type: array
                    items:
                      type: string
                    description: 所需输入参数名称的数组
                    example:
                      - 预算
                      - 兴趣
                      - 持续时间
                      - 年龄
        examples:
          travel_crew:
            summary: 旅行规划团队输入参数
            value:
              inputs:
                - 预算
                - 兴趣
                - 持续时间
                - 年龄
          outreach_crew:
            summary: 外联团队输入参数
            value:
              inputs:
                - 姓名
                - 职位
                - 公司
                - 行业
                - 我们的产品
                - LinkedIn链接
        description: 成功检索所需输入参数
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
                    description: 详细错误信息
                    example: 提供的 bearer 令牌无效
            refIdentifier: '#/components/schemas/Error'
        examples:
          example:
            value:
              error: 未授权
              message: Bearer 令牌无效或缺失
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
              error: 未找到
              message: 未找到请求的资源
        description: 未找到资源
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