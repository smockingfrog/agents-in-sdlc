---
title: "练习 5：迭代改进 GitHub Copilot 的工作"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

## 审查工作成果

在整个实验中，你与 GitHub Copilot 一起处理了多个任务，以改进用户体验并添加功能。你让 Copilot 为代码添加文档、为设计团队构建相关游戏功能，并实现高对比度和亮色模式切换等无障碍功能。现在来检查代码变更；如有需要，也可以提供反馈，帮助 Copilot 改进成果。

### 场景

正如前面多次强调的那样，生成式 AI 并不会改变软件设计和 DevOps 的基本原则。我们始终需要审查生成的代码，并遵循常规 DevOps 流程。因此，在开启团队其他成员的审查之前，先审查 GitHub Copilot 为代码文档、相关游戏功能和无障碍功能提供的建议。

## 安全与 GitHub Copilot 云智能体

由于 Copilot 云智能体会异步且无人监督地执行任务，因此设置了一些安全限制，以确保操作安全。这些限制包括：

- Copilot 对你的存储库只有读取权限，对工作分支只有写入权限。
- 云智能体在 GitHub Actions 中运行，并创建一个独立的临时环境来工作。
- GitHub Actions 工作流需要先由人工批准才能运行。
- 默认情况下，[对外部资源的访问受到限制](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)，MCP 服务器也包括在内。

## 审查生成的文档

先查看 GitHub Copilot 云智能体生成的第一个拉取请求 (PR)——为代码添加文档。你将在 GitHub.com 上使用标准 PR 界面完成此任务。

> [!NOTE]
> 查看 PR 时，可能会看到 GitHub Copilot 被防火墙阻止的警告。这是**预期行为**，因为 Copilot 默认无法访问大多数外部资源，包括外部 MCP 服务器。如果需要，可以[自定义或禁用 Copilot 云智能体的防火墙][agent-firewall]。

1. 返回 github.com 上的存储库。
2. 选择 **Pull Requests**，打开拉取请求列表。
3. 打开标题类似于 **Add missing documentation** 的拉取请求。

> [!NOTE]
> 如果 Copilot 仍在处理任务，拉取请求会带有 **[WIP]** 标记。此时请等待 Copilot 完成工作。这可能需要几分钟；你可以休息一下，或回顾迄今为止学到的内容。

4. 拉取请求准备就绪后，选择 **Files changed** 选项卡并审查变更。

    ![Files changed 选项卡](../../../_images/shared-pr-files-changed.png)

5. 查看新更新的代码，其中包括新生成的 TSDoc 文档注释和其他文档。具体变更可能各不相同。

    浏览变更时，留意 TSDoc 文档注释、TypeScript 约定和注释头。这些内容来自你在练习 2 开始时审查的自定义指令文件。

6. 审查更新并确认一切正常后，返回 **Conversation** 选项卡并向下滚动。
7. 你应该会看到一些工作流正在等待批准的提示。
8. 如果有工作流等待批准，请选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)
9. 你应该会看到工作流进入拉取请求检查队列。如果一切正常，单个 Astro 应用的项目检查就会通过。这可能需要几分钟。

## 请求 GitHub Copilot 修改

与 Copilot 协作并非单向过程。你可以像在拉取请求中提及团队其他成员一样，在评论或代码行内评论中提及 Copilot。Copilot 会读取评论，并启动新会话来处理请求。由于结果具有非确定性，我们无法提供固定的请求文本。可以让 Copilot：

- 在每个代码文件顶部添加简短的注释头，说明文件用途。
- 为 TypeScript 和 Astro 文件添加 TSDoc 文档注释。
- 创建 README，介绍 Astro 应用的结构。

1. 添加评论，请求修改生成的文档，并像提及用户一样标记 **@copilot**。可以使用上面的任一建议，也可以提出其他希望为代码库添加的文档。
2. 选择 **View Session**，观察 Copilot 执行工作。留意 Copilot 如何启动新会话来应用修改。
3. 可以选择 **Back to pull request** 返回拉取请求。

    ![Back to pull request](../../../_images/ex7-back-to-pr.png)

4. Copilot 完成修改后，拉取请求中应该会出现新的提交。
5. 选择 **Files changed** 选项卡，审查变更。

可以继续迭代，直到满意为止。满意后，可以将 PR 从草稿转换为准备审查状态，再合并到主分支。

![将 PR 转为准备审查状态](../../../_images/ex7-ready-for-review.png)

## 审查相关游戏功能

接下来返回 Copilot 为解决“在游戏详情页显示相关游戏”问题而创建的 PR。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似于 **Show related games on the game details page** 的 PR。
4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 审查更新并确认一切正常后，返回 **Conversation** 选项卡并向下滚动。
6. 你应该会看到一些工作流正在等待批准的提示。
7. 如果有工作流等待批准，请选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)
8. 你应该会看到工作流进入拉取请求检查队列。如果一切正常，单个 Astro 应用的项目检查就会通过。这可能需要几分钟。
9. **可选：**你也可以在 Codespace 中切换到此分支，手动测试相关游戏功能。导航到 Codespace，打开终端并运行以下命令（将 `<branch-name>` 替换为 Copilot 创建的分支名称，例如 **copilot/fix-8**）：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

Copilot 已构建相关游戏功能！和之前一样，你可以通过迭代与 Copilot 云智能体协作并请求更新。例如，可以要求 Copilot 调整显示的相关游戏数量，或者要求添加注释头和 TSDoc 文档注释（请记住——此任务是在你更新自定义指令**之前**分配的！）。像之前一样，你可以在 **Conversation** 选项卡中添加评论来提出请求；Copilot 会读取评论并启动新会话。

## 审查无障碍功能

最后来审查使用无障碍自定义智能体实现的功能。此 PR 应同时包含你在练习 3 中分配的高对比度模式，以及你在练习 4 的任务控制中心中提出的亮色模式。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似于 **Add high contrast mode to website** 的 PR。

> [!NOTE]
> 如果 Copilot 仍在处理任务，拉取请求会带有 **[WIP]** 标记。此时请等待 Copilot 完成工作。这可能需要几分钟。

4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 审查实现，特别留意：
   - 用于切换模式的 UI 组件
   - 用于保存用户偏好的本地存储
   - 高对比度和亮色模式的 CSS 或样式变更
   - 无障碍属性（ARIA 标签、键盘导航等）
   - 管理模式切换的 JavaScript/TypeScript 代码

6. 审查更新并确认一切正常后，返回 **Conversation** 选项卡并向下滚动。
7. 你应该会看到一些工作流正在等待批准的提示。
8. 如果有工作流等待批准，请选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)
9. 你应该会看到工作流进入拉取请求检查队列。如果一切正常，单个 Astro 应用的项目检查就会通过。这可能需要几分钟。
10. **可选：**你可以在 Codespace 中切换到此分支，手动测试无障碍功能。导航到 Codespace，打开终端并运行以下命令（将 `<branch-name>` 替换为 Copilot 创建的分支名称）：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    然后启动应用，在浏览器中测试高对比度和亮色模式切换，并确认设置在重新加载页面后仍然保留。

注意，无障碍自定义智能体帮助 Copilot 遵循无障碍最佳实践来实现这些功能。如果发现无障碍方面的问题或改进点，可以像处理之前的 PR 一样，在评论中标记 **@copilot** 请求更新。

## 可选练习 — 继续委派任务

当你能把真实待办事项交给云智能体并处理其他工作时，它最有用。可以在自己的存储库中创建更多 issue 并分配给 Copilot，以养成这种习惯。以下是一些建议：

- 在游戏详情页创建支持者兴趣表单。
- 在游戏列表页实现分页。
- 为数据访问辅助函数添加输入验证和错误处理。

## 总结

你已完成云智能体练习。在这些课程中，你：

- **检查了存储库自带的自定义指令文件**，以便之后了解它们对云智能体输出的影响。
- **将 issue 分配给 Copilot 云智能体**，观察它设置环境、制定计划并异步执行任务。
- **创建并使用自定义智能体**实现无障碍功能，添加高对比度和亮色模式切换。
- **使用 Copilot 智能体页面作为任务控制中心**，监控并引导无障碍会话。
- **审查并迭代改进云智能体的拉取请求**，通过标记 `@copilot` 请求修改并批准工作流。

## 回顾和后续步骤

你已完成云智能体练习。想继续探索的话，其他练习可以补充你在这里学到的内容：

- 🖥️ **[VS Code 练习](../../vscode/)** — 在 IDE 中探索 Copilot Chat 智能体模式和 MCP 集成。
- 💻 **[CLI 练习](../../cli/)** — 在终端中交付两项经过审查的变更，使用 Plan 和 Autopilot 模式、智能体技能、自定义 QA 智能体、Playwright MCP、Agent Merge，以及可选的 `/delegate` 云智能体桥接功能。

在自己的存储库中，可以尝试以下后续练习：

- 将重构或测试覆盖率 issue 分配给云智能体。
- 为其他领域创建自定义智能体。
- 为其他技术栈设置 `copilot-setup-steps.yml`。

## 资源

- [GitHub Copilot][github-copilot]
- [关于 Copilot 智能体][copilot-agents]
- [将 GitHub issue 分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]
- [配置 Copilot 云智能体防火墙][agent-firewall]

---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
