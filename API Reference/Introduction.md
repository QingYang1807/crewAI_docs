# 简介

> CrewAI AMP REST API 完整参考文档

# CrewAI AMP API

欢迎使用 CrewAI AMP API 参考文档。此 API 允许您以编程方式与已部署的 crew（智能体团队）进行交互，从而将其集成到您的应用程序、工作流和服务中。

## 快速开始

<Steps>
  <Step title="获取您的 API 凭据">
    在 CrewAI AMP 控制台中，导航到您的 crew 详情页，然后从“状态”选项卡中复制您的 Bearer Token。
  </Step>

  <Step title="发现所需输入">
    使用 `GET /inputs` 端点来查看您的 crew 需要哪些参数。
  </Step>

  <Step title="启动 Crew 执行">
    调用 `POST /kickoff` 端点并传入您的输入参数，以启动 crew 执行并接收一个 `kickoff_id`。
  </Step>

  <Step title="监控进度">
    使用 `GET /status/{kickoff_id}` 来检查执行状态并检索结果。
  </Step>
</Steps>

## 身份验证

所有 API 请求都需要使用 Bearer token 进行身份验证。请在 `Authorization` 请求头中包含您的 token：

```bash  theme={null}
curl -H "Authorization: Bearer YOUR_CREW_TOKEN" \
  https://your-crew-url.crewai.com/inputs
```

### Token 类型

| Token 类型             | 作用域                     | 使用场景                                                     |
| :--------------------- | :------------------------ | :----------------------------------------------------------- |
| **Bearer Token**       | 组织级别访问               | 完整的 crew 操作，适用于服务器到服务器的集成                 |
| **User Bearer Token**  | 用户级别访问               | 权限受限，适用于用户特定的操作                               |

<Tip>
  您可以在 CrewAI AMP 控制台中 crew 详情页的“状态”选项卡中找到这两种 token。
</Tip>

## 基础 URL

每个已部署的 crew 都有其自己唯一的 API 端点：

```
https://your-crew-name.crewai.com
```

请将 `your-crew-name` 替换为您控制台中实际的 crew URL。

## 典型工作流

1.  **发现**：调用 `GET /inputs` 以了解您的 crew 需要什么。
2.  **执行**：通过 `POST /kickoff` 提交输入以开始处理。
3.  **监控**：轮询 `GET /status/{kickoff_id}` 直到完成。
4.  **结果**：从完成的响应中提取最终输出。

## 错误处理

该 API 使用标准的 HTTP 状态码：

| 代码  | 含义                                     |
| ----- | :--------------------------------------- |
| `200` | 成功                                     |
| `400` | 错误请求 - 输入格式无效                  |
| `401` | 未授权 - Bearer token 无效               |
| `404` | 未找到 - 资源不存在                      |
| `422` | 验证错误 - 缺少必需的输入                |
| `500` | 服务器错误 - 请联系支持                  |

## 交互式测试

<Info>
  **为什么没有“发送”按钮？** 由于每个 CrewAI AMP 用户都有自己唯一的 crew URL，我们使用 **参考模式** 而不是交互式测试环境，以避免混淆。这能准确地向您展示请求应有的样子，而没有无功能的发送按钮。
</Info>

每个端点页面都会向您展示：

* ✅ **精确的请求格式**，包含所有参数
* ✅ **响应示例**，包括成功和错误情况
* ✅ **多种语言的代码示例**（cURL、Python、JavaScript 等）
* ✅ **身份验证示例**，包含正确的 Bearer token 格式

### **测试您的实际 API：**

<CardGroup cols={2}>
  <Card title="复制 cURL 示例" icon="terminal">
    复制 cURL 示例，并将 URL 和 token 替换为您的真实值。
  </Card>

  <Card title="使用 Postman/Insomnia" icon="play">
    将示例导入到您首选的 API 测试工具中。
  </Card>
</CardGroup>

**示例工作流：**

1.  从任意端点页面**复制此 cURL 示例**。
2.  将 `your-actual-crew-name.crewai.com` **替换为您真实的 crew URL**。
3.  将 Bearer token **替换为您从控制台获取的真实 token**。
4.  在您的终端或 API 客户端中**运行请求**。

## 需要帮助？

<CardGroup cols={2}>
  <Card title="企业支持" icon="headset" href="mailto:support@crewai.com">
    获取关于 API 集成和故障排除的帮助。
  </Card>

  <Card title="企业控制台" icon="chart-line" href="https://app.crewai.com">
    管理您的 crew 并查看执行日志。
  </Card>
</CardGroup>