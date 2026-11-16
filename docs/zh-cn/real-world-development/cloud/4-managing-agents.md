---
title: "练习 4 - 监控和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

在前几项练习中，你让 Copilot 云智能体处理了三项不同任务，旨在改进用户体验并添加功能。云智能体能够异步自主工作，但监控这些任务仍然很重要。

你可以使用多种工具管理分配给云智能体的任务，包括 GitHub.com 上的[智能体页面][agents-page]。在这个任务控制中心，你可以查看所有具有开放拉取请求 (PR) 的智能体任务，了解已执行的操作，甚至引导进行中的会话。

本课将学习如何：

- 探索智能体页面，监控云智能体任务。
- 引导正在进行的会话，请求添加更多功能。

## 场景

将任务分配给智能体以创建高对比度模式后，团队意识到同时添加亮色模式也很合适。既然正在更新网站样式并添加切换功能，就可以一并实现。你将引导智能体，让它同时添加亮色模式和高对比度模式。

## 查看 Copilot 云智能体任务

查看当前分配给 Copilot 云智能体的所有任务的状态。

1. 导航到 [Copilot 智能体页面](https://github.com/copilot/agents)。
2. 留意主面板和左侧面板中的任务列表。你应该会看到已分配给 Copilot 的任务，包括：
    - 更新代码库文档。
    - 生成用于修改游戏的 API。
    - 为网站添加高对比度模式。
3. 选择一个正在运行的任务，审查 Copilot 已执行的工作。可能包括：
    - 从存储库检出代码。
    - 为 Copilot 创建环境。
    - 设置 MCP 服务器。
    - 完成任务所执行的各项步骤。

> [!NOTE]
> 显示的具体步骤会根据 Copilot 的工作状态和采用的方案而变化。

4. 同时留意右侧出现的 PR 面板。你可以在这里查看 PR 和已更改的文件，以便进一步监控。

## 引导云智能体

了解当前活动任务后，趁 Copilot 正在处理高对比度模式，请它同时添加亮色模式切换开关。

1. 选择与添加高对比度模式相关的会话。确切标题可能会因 Copilot 使用的名称和当前工作状态而异。

    ![任务控制中心中的无障碍会话](../../../_images/ex6-accessibility-session.png)

2. 监控会话几分钟，直到会话显示已完成设置并开始工作。出现类似下方的消息时，就表示已开始。
3. 在 **Steer active session while Copilot is working** 对话框中，添加以下提示：

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![智能体页面中的云智能体任务，突出显示 Steer active session while Copilot is working 对话框](../../../_images/ex6-steer-cloud-agent-task.png)

4. 按 <kbd>Enter</kbd> 发送提示。
5. 留意 Copilot 如何确认收到提示并将其纳入工作流程。

## 让 Copilot 完成工作

和之前一样，Copilot 会继续处理更新后的任务！在你发送消息时，它可能正在执行某一步；完成该步骤后，它会将新请求纳入工作流程。

这同样需要几分钟，可以借此暂停一下，回顾目前学到和探索的内容。

## 总结和后续步骤

本课介绍了 Copilot 智能体页面，它是监控和引导 GitHub Copilot 云智能体任务的中心。你可以使用此任务控制中心跟踪所有进行中和已完成的任务、审查正在执行的工作，甚至重定向进行中的任务以调整范围或补充指导。

本课介绍了以下概念：

- 使用任务控制中心和智能体页面监控云智能体任务。
- 重定向正在进行的会话，请求添加更多功能。

Copilot 即将完成无障碍功能，现在可以继续[迭代 Copilot 创建的拉取请求][next-lesson]。

## 资源

- [Copilot 智能体页面][agents-page]
- [关于自定义智能体][custom-agents]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代改进 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
