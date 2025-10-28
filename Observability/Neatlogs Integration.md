# Neatlogs 集成

> 了解、调试和分享您的 CrewAI 智能体运行过程

# 简介

Neatlogs 帮助您**查看智能体做了什么**、**为什么这样做**，并**分享这些过程**。

它捕获每一个步骤：思考、工具调用、响应、评估。没有原始日志，只有清晰、结构化的跟踪记录。非常适合调试和协作。

## 为什么使用 Neatlogs？

CrewAI 智能体使用多种工具和推理步骤。当出现问题时，您需要的是上下文——而不仅仅是错误信息。

Neatlogs 让您能够：

* 跟踪完整的决策路径
* 直接在步骤上添加反馈
* 使用 AI 助手与跟踪记录聊天
* 公开分享运行过程以获取反馈
* 将洞察转化为任务

所有功能集于一处。

轻松管理您的跟踪记录

<img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-1.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=d01a5ce64066c6c7387b238068e71369" alt="跟踪记录" data-og-width="1999" width="1999" data-og-height="763" height="763" data-path="images/neatlogs-1.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-1.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=ee62fda86fa566c25c133bcab4749395 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-1.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=5cb6eaca0429f7e70bb5c8d98a489a97 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-1.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=cb664845151f8e54c0e0b9fba753f383 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-1.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=fb471833d13ba8718ebd37cc6f557697 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-1.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=e470693ab78a2cce5b34570b328c6939 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-1.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=2211c7cdbf87f4e96de3aa5a51927b1d 2500w" />
<img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-2.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=5b737699468781be25098c33040d2125" alt="跟踪响应" data-og-width="1999" width="1999" data-og-height="1128" height="1128" data-path="images/neatlogs-2.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-2.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=020336c536f38ce54dfc04854acac7d4 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-2.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=8a40138ff848d453607b8e4cf6d0af31 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-2.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=411d496952511260f03dcf703cf40402 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-2.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=ce7a99a7d6752ae77706cde411104694 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-2.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=e943a0308341c59d6b4d17e29e17126c 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-2.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=61b95cdb68c4c5cbdd349e802db3f2cb 2500w" />

查看 CrewAI 跟踪记录的最佳用户体验。可在任何位置发表评论。使用 AI 进行调试。

<img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-3.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=43cda9bcd83376dda4523ff0596b2043" alt="跟踪详情" data-og-width="1999" width="1999" data-og-height="1125" height="1125" data-path="images/neatlogs-3.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-3.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=b412fc1111d110fba24398449f86c8a6 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-3.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=bc9a8210c617335893a0b9e94b9dcede 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-3.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=bca4c7758110744a457e3e635ba86e1c 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-3.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=c259898ac4cbe4835a0df33f161c7840 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-3.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=74f03053e7cc5b98b3e568417de3a319 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-3.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=b90e2b8fcadb097c82a60e6522533386 2500w" />
<img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-4.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=c9e7ad0653cae7bfaad2dd448d90eda0" alt="与跟踪记录聊天的 AI 机器人" data-og-width="1999" width="1999" data-og-height="751" height="751" data-path="images/neatlogs-4.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-4.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=eb0debf5272db5db3729d8b4b4634d94 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-4.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=9ebccf5654ad590f1d231118b4a29037 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-4.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=a7987df251bd7085c86535c31c3bc8fe 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-4.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=113e589438936a55df794a60faec5ff7 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-4.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=469f0ab2f09cdd65c18e925ebd88be11 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-4.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=55115f959b3f2e49231e9ed273e6d11c 2500w" />
<img src="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-5.png?fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=a977655abb8cd26d9ed4cef5fdd7d859" alt="评论抽屉" data-og-width="1999" width="1999" data-og-height="1388" height="1388" data-path="images/neatlogs-5.png" data-optimize="true" data-opv="3" srcset="https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-5.png?w=280&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=51ad567b077e31082ed8f2a1c53be446 280w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-5.png?w=560&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=b4c663fe1527dc74a13e8c7a7ae955d2 560w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-5.png?w=840&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=fdddfe615d4098db90f694707d70ec87 840w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-5.png?w=1100&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=15cefd5838432e622844dced45f2f6b6 1100w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-5.png?w=1650&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=18b726e6b4bf38ee419f2a50be1e748a 1650w, https://mintcdn.com/crewai/qVjgZHKAyEOgSSUS/images/neatlogs-5.png?w=2500&fit=max&auto=format&n=qVjgZHKAyEOgSSUS&q=85&s=52fcf26f16e0d4177dbcb9c0da5d1bb9 2500w" />

## 核心功能

* **跟踪记录查看器**：按顺序跟踪思考、工具和决策
* **内联评论**：在任何跟踪记录步骤上标记团队成员
* **反馈与评估**：将输出标记为正确或错误
* **错误高亮**：自动标记 API/工具故障
* **任务转换**：将评论转换为已分配的任务
* **询问跟踪记录（AI）**：使用 Neatlogs AI 机器人与您的跟踪记录聊天
* **公开分享**：向您的社区发布跟踪记录链接

## 与 CrewAI 快速设置

<Steps>
  <Step title="注册并获取 API 密钥">
    访问 [neatlogs.com](https://neatlogs.com/?utm_source=crewAI-docs)，创建项目，复制 API 密钥。
  </Step>

  <Step title="安装 SDK">
    ```bash  theme={null}
    pip install neatlogs
    ```

    （最新版本 0.8.0，支持 Python 3.8+；MIT 许可证）
  </Step>

  <Step title="初始化 Neatlogs">
    在启动 Crew 智能体之前，添加：

    ```python  theme={null}
    import neatlogs
    neatlogs.init("YOUR_PROJECT_API_KEY")
    ```

    智能体照常运行。Neatlogs 会自动捕获所有内容。
  </Step>
</Steps>

## 内部原理

根据 GitHub 信息，Neatlogs：

* 捕获思考、工具调用、响应、错误和令牌统计
* 支持人工智能驱动的任务生成和强大的评估工作流

所有这些只需两行代码。

## 观看其工作演示

### 🔍 完整演示（4 分钟）

<iframe className="w-full aspect-video rounded-xl" src="https://www.youtube.com/embed/8KDme9T2I7Q?si=b8oHteaBwFNs_Duk" title="NeatLogs 概述" frameBorder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowFullScreen />

### ⚙️ CrewAI 集成（30 秒）

<iframe className="w-full aspect-video rounded-xl" src="https://www.loom.com/embed/9c78b552af43452bb3e4783cb8d91230?sid=e9d7d370-a91a-49b0-809e-2f375d9e801d" title="Loom 视频播放器" frameBorder="0" allowFullScreen />

## 链接与支持

* 📘 [Neatlogs 文档](https://docs.neatlogs.com/)
* 🔐 [仪表板与 API 密钥](https://app.neatlogs.com/)
* 🐦 [在 Twitter 上关注](https://twitter.com/neatlogs)
* 📧 联系方式：[hello@neatlogs.com](mailto:hello@neatlogs.com)
* 🛠 [GitHub SDK](https://github.com/NeatLogs/neatlogs)

## 太长不看

只需：

```bash  theme={null}
pip install neatlogs

import neatlogs
neatlogs.init("YOUR_API_KEY")

您现在就可以在几秒钟内捕获、理解、分享并针对您的 CrewAI 智能体运行过程采取行动。
无需设置开销。完全透明的跟踪记录。完整的团队协作。
```