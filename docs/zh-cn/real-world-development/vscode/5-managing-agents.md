---
title: "练习 5 - 监控和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义智能体][previous-lesson] |
|:--|

启用 GitHub Copilot 智能体模式后，它会自主探索代码库、提出变更、编辑文件并运行命令。由于这些工作会在你的计算机上实时进行，VS Code 中的 Copilot Chat 会持续显示每次工具调用和文件编辑。你可以直接在聊天中审查每个差异、接受或拒绝单项变更，并通过后续提示引导对话、改进或扩展工作，全程无需离开编辑器。

本练习将学习如何：

- 实时监控无障碍智能体的工作。
- 审查并接受提议的文件编辑。
- 在会话进行中引导智能体，除了高对比度模式之外再添加亮色模式切换。

## 场景

你在[练习 4][previous-lesson]中使用的无障碍自定义智能体正在实现高对比度模式。工作进行期间，设计团队提出了额外需求——为觉得默认主题太暗的用户添加亮色模式切换。你不必从头开始新对话，可以引导现有会话扩展工作。

## 监控 Copilot 的进度

Copilot Chat 视图会显示智能体执行的每一步，包括探索的文件、运行的命令和提出的编辑。每次文件编辑都会在聊天中显示差异和 **Keep** / **Undo** 控件。

1. 从练习 4 返回 Copilot Chat 视图。
2. 智能体工作时，滚动聊天内容，观察每次工具调用。你会看到文件读取、文件编辑以及需要它运行的终端命令。
3. 智能体提出文件编辑时，差异会内嵌显示：
    - 选择 **Keep** 接受变更。
    - 选择 **Undo** 撤销变更，让智能体重新处理。
4. 也可以打开聊天顶部的 **Files changed** 指示器，查看该会话中智能体修改的所有文件。

> [!NOTE]
> 如果工具调用请求权限（例如运行终端命令），智能体会暂停并显示 **Allow** 提示。仅批准你认可的命令。

5. 等待智能体完成高对比度模式的实现。完成时会显示总结变更的最终消息。

## 引导智能体添加亮色模式

现在高对比度模式已就绪，可以在同一对话中扩展功能，添加亮色模式切换。继续使用同一聊天会话，智能体会保留先前工作的上下文，包括修改的组件、使用的样式方案和设置的本地存储模式。

1. 在 Copilot Chat 输入框中发送以下后续提示：

    ```plaintext
    Nice work! Now add a light-mode toggle alongside the high-contrast toggle. Light mode should follow the same pattern — persist via local storage and be accessible from the same area of the UI. The user should be able to enable high-contrast and light mode independently.
    ```

2. 智能体会继续之前的工作，探索已有变更并提出亮色模式的修改。
3. 和高对比度模式一样，逐项审查新编辑。使用 **Keep** 或 **Undo** 处理每个差异。
4. 如果智能体的方向不合适（例如以不符合你预期的方式重构切换 UI），可以选择聊天输入框旁的 **Stop** 按钮停止任务，然后发送澄清提示。

> [!TIP]
> 引导进行中的会话比重新开始更快。可以使用后续提示提出调整要求（“简化 CSS——使用 CSS 变量，而不是重复选择器”）、纠正方向（“不要添加第三个开关，将亮色模式整合到现有设置面板中”），或扩展范围（“还要为两个开关添加键盘快捷键”）。

## 审查工作集

提交工作前，快速检查智能体修改的所有内容。

1. 在 VS Code 中打开 **Source Control** 视图。
2. 审查所有已更改的文件。你应该会看到 Astro 组件、样式和相关测试的更新。
3. 打开几个已更改的文件并查看差异。确认自定义智能体要求的无障碍模式已经体现在变更中，包括 ARIA 属性、键盘导航、语义化 HTML 和本地存储。
4. 确认满意后，在 Source Control 面板中暂存并提交变更。下一课将发布这些变更。

## 总结和后续步骤

你已使用 Copilot Chat 智能体模式监控并引导正在进行的会话——逐项审查文件编辑、接受或拒绝变更，并通过后续提示扩展工作，同时保留原有上下文。

本课介绍了以下概念：

- 实时监控智能体工作。
- 接受或拒绝单项文件编辑。
- 通过后续提示引导进行中的会话。

下一课将[发布无障碍功能变更并审查本地自定义智能体在实验中生成的内容][next-lesson]。

## 资源

- [VS Code 中的 Copilot Chat][copilot-chat-vscode]
- [使用智能体模式][agent-mode]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代改进 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../4-custom-agents/
[next-lesson]: ../6-iterating/
[copilot-chat-vscode]: https://code.visualstudio.com/docs/copilot/chat/copilot-chat
[agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
