# 使用 DALL-E 进行图像生成

> 了解如何在 CrewAI 项目中使用 DALL-E 进行 AI 驱动的图像生成

CrewAI 支持与 OpenAI 的 DALL-E 集成，允许您的 AI 智能体作为其任务的一部分生成图像。本指南将引导您完成如何在 CrewAI 项目中设置和使用 DALL-E 工具。

## 先决条件

* 已安装 crewAI（最新版本）
* 具有 DALL-E 访问权限的 OpenAI API 密钥

## 设置 DALL-E 工具

<Steps>
  <Step title="导入 DALL-E 工具">
    ```python  theme={null}
    from crewai_tools import DallETool
    ```
  </Step>

  <Step title="将 DALL-E 工具添加到您的智能体配置中">
    ```python  theme={null}
    @agent
    def researcher(self) -> Agent:
        return Agent(
            config=self.agents_config['researcher'],
            tools=[SerperDevTool(), DallETool()],  # 将 DallETool 添加到工具列表中
            allow_delegation=False,
            verbose=True
        )
    ```
  </Step>
</Steps>

## 使用 DALL-E 工具

将 DALL-E 工具添加到您的智能体后，它可以根据文本提示生成图像。该工具将返回生成图像的 URL，该 URL 可用于智能体的输出或传递给其他智能体进行进一步处理。

### 智能体配置示例

```yaml  theme={null}
role: >
    LinkedIn 资深数据研究员
goal: >
    根据提供的姓名 {name} 和领域 {domain} 查找详细的 LinkedIn 资料
    根据领域 {domain} 生成 Dall-e 图像
backstory: >
    您是一位经验丰富的研究员，擅长发现最相关的 LinkedIn 资料。
    以高效浏览 LinkedIn 而闻名，您擅长清晰简洁地收集和展示专业信息。
```

### 预期输出

配备 DALL-E 工具的智能体将能够生成图像并在其响应中提供 URL。然后您可以下载该图像。

<Frame>
  <img src="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/dall-e-image.png?fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=7b6378a1ee0aad5d3941193c6802312c" alt="DALL-E 图像" data-og-width="670" width="670" data-og-height="670" height="670" data-path="images/enterprise/dall-e-image.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/dall-e-image.png?w=280&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=95b3ae8ec53f789746846831fa981b32 280w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/dall-e-image.png?w=560&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=f880f86fa3b648a257ac74fcc7838dce 560w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/dall-e-image.png?w=840&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=c98e8fd36d462d3806c398c1f074efb2 840w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/dall-e-image.png?w=1100&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=68af7edd51913d04723c0fbae774ea1d 1100w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/dall-e-image.png?w=1650&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=07c15be8399f83239d49e28f6667a28d 1650w, https://mintcdn.com/crewai/5SZbe87tsCWZY09V/images/enterprise/dall-e-image.png?w=2500&fit=max&auto=format&n=5SZbe87tsCWZY09V&q=85&s=619edc0ffc57a1eddd3cd7f9715b6b0a 2500w" />
</Frame>

## 最佳实践

1. **在图像生成提示中要具体**以获得最佳效果。
2. **考虑生成时间** - 图像生成可能需要一些时间，因此请将这一点纳入您的任务计划中。
3. **遵循使用政策** - 生成图像时始终遵守 OpenAI 的使用政策。

## 故障排除

1. **检查 API 访问权限** - 确保您的 OpenAI API 密钥具有 DALL-E 的访问权限。
2. **版本兼容性** - 检查您是否使用最新版本的 crewAI 和 crewai-tools。
3. **工具配置** - 验证 DALL-E 工具是否已正确添加到智能体的工具列表中。