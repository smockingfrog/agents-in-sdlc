---
title: "练习 3 - 使用 Playwright MCP 服务器测试功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：智能体模式][previous-lesson] · [下一课：自定义智能体 →][next-lesson]

你刚使用智能体模式构建了筛选功能。创建拉取请求前，应确认功能确实能在浏览器中正常运行。你不必自己逐项操作应用，而是可以连接 **Playwright MCP 服务器**，让 Copilot 操作真实浏览器测试功能，然后发布分支并创建 PR。

本练习将学习如何：

- 了解 Model Context Protocol (MCP) 以及 MCP 服务器如何使用新工具扩展 Copilot。
- 将 Playwright MCP 服务器添加到工作区。
- 请 Copilot 使用它在浏览器中手动测试筛选功能。
- 发布分支，并为筛选工作创建拉取请求。

## 什么是 Model Context Protocol (MCP)？

当智能体模式能够访问编辑器之外的资源时，其功能会强大得多。Copilot 通过 Model Context Protocol (MCP) 实现这一点——MCP 是智能体与外部工具和服务通信的标准方式。

![示意图显示智能体模式的内部工作方式，以及它如何与上下文、LLM 和工具交互，其中包括 MCP 服务器和 VS Code 扩展提供的工具](../../../_images/ex1-mcp-diagram.png)

[Model Context Protocol (MCP)](https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/) 为 AI 智能体提供与外部工具和服务通信的方式。借助 MCP，AI 智能体可以实时与外部工具和服务通信，从而访问最新信息（使用资源）并代表你执行操作（使用工具）。

这些工具和资源通过 MCP 服务器访问。MCP 服务器充当 AI 智能体与外部工具及服务之间的桥梁，负责管理智能体与外部工具（例如现有 API 或 NPM 包等本地工具）之间的通信。每个 MCP 服务器都代表 AI 智能体可访问的一组工具和资源。

常见的 MCP 服务器包括：

- **[GitHub MCP Server](https://github.com/github/github-mcp-server)**：提供一组管理 GitHub 存储库的 API，让 AI 智能体可以创建新存储库、更新现有存储库、管理 issue 和拉取请求等。
- **[Playwright MCP Server](https://github.com/microsoft/playwright-mcp)**：使用 Playwright 提供浏览器自动化能力，让 AI 智能体可以浏览网页、填写表单和选择按钮等。

还有许多其他 MCP 服务器提供不同工具和资源。GitHub 提供了一个 [MCP 注册表](https://github.com/mcp)，方便发现和参与生态系统。

> [!CAUTION]
> 从安全角度来看，请像对待项目中的其他依赖项一样对待 MCP 服务器。使用前应仔细审查其源代码、验证发布者并考虑安全影响。只使用你信任的 MCP 服务器，并谨慎授予其访问敏感资源或执行敏感操作的权限。

## 检查 MCP 配置

`.vscode/mcp.json` 文件用于配置 VS Code 工作区可用的 MCP 服务器。

1. 在 Codespace 中打开 `.vscode/mcp.json`。
2. 你应该会看到已配置好的 `github` 服务器：

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        }
      }
    }
    ```

项目模板已包含 `github` 条目，因此 Copilot 在上一练习中可以读取 issue 待办列表。它使用[远程 GitHub MCP 服务器][remote-github-mcp-server]，无需本地安装——VS Code 通过 HTTP 连接服务器，并在 Copilot 首次使用其工具时要求你登录 GitHub。

## 添加 Playwright MCP 服务器

现在添加第二台服务器。[Playwright MCP 服务器][playwright-mcp-server]可以为 Copilot 提供可控的浏览器，正适合用于测试功能。

1. 在 `.vscode/mcp.json` 中，在 `github` 旁边添加 `playwright` 条目，使文件如下所示：

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        },
        "playwright": {
          "command": "npx",
          "args": ["@playwright/mcp@latest", "--headless"]
        }
      }
    }
    ```

2. 保存文件。

与远程的 `github` 服务器不同，`playwright` 是**本地**服务器：VS Code 会在你的计算机上运行 `npx @playwright/mcp@latest` 来启动它。`--headless` 标记让 Playwright 在后台运行浏览器，不显示可见窗口。Codespace 中没有桌面环境，因此必须使用此标记。

> [!NOTE]
> Tailspin Toys 项目已在端到端测试中使用 Playwright，因此通常已经安装了 Playwright 所需的浏览器。如果 Copilot 后续报告缺少浏览器，请让它运行 `npx playwright install chromium`，然后重试。

## 启动并信任服务器

VS Code 会**按需**启动 MCP 服务器——Copilot 首次需要服务器工具时，VS Code 会启动服务器并询问你是否信任它。

1. 在 `.vscode/mcp.json` 中，`playwright` 条目上方会出现 **Start** 操作。选择它立即启动服务器，并确认配置正确。
2. 如果 VS Code 询问是否信任服务器，请检查配置并选择信任，以便启动服务器。

手动启动是可选的。如果跳过此步骤，VS Code 会在下一步中 Copilot 首次需要工具时启动服务器，并询问是否信任它。

## 测试筛选功能

[Playwright MCP 服务器][playwright-mcp-server]可让 Copilot 操作真实浏览器。你不必自己点击应用检查工作，智能体可以打开页面、导航、应用筛选、读取结果并向你总结——这是在不离开对话的情况下，快速确认功能符合预期的方式。

在内部，Playwright MCP 服务器使用页面的[无障碍树][playwright-mcp-server]，而非屏幕截图。这样智能体就可以基于结构化且带标签的元素（按钮、链接、列表项）进行推理，与辅助技术的工作方式一致。因此，快速功能检查也能同时完成一次简单的无障碍检查。

连接服务器并启动应用后，请 Copilot 测试刚才构建的筛选功能：

```text
Using the Playwright MCP server, open a browser to the running app at http://localhost:4321 and verify the new game filtering feature:

1. Go to the games page and note how many games are listed.
2. Apply a category filter and confirm the list updates to only show games in that category.
3. Clear it, then apply a publisher filter and confirm the list updates to that publisher.
4. Combine a category and a publisher filter and confirm the results respect both.

Report what you observe at each step, and call out anything that does not behave as expected.
```

Copilot 会通过 Playwright MCP 服务器启动浏览器，逐步执行测试并报告结果。根据 issue 中的验收标准审查报告；如果发现问题，可以提出后续问题，或要求 Copilot 在创建拉取请求前修复代码。

> [!NOTE]
> 应用必须在 `http://localhost:4321` 运行才能进行测试。如果你已停止开发服务器，请在发送提示前重新启动。Copilot 首次使用 Playwright MCP 服务器时，可能需要下载浏览器；如果报告缺少浏览器，请让它运行 `npx playwright install chromium` 并重试。

[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp

## 发布分支并创建拉取请求

确认功能正常后，就可以创建拉取请求 (PR)，供团队审查了。首先发布 `filtering-vscode` 分支。

1. 导航到 Codespace 中的 **Source Control** 面板，审查 Copilot 所做的变更。
2. 选择 **+** 图标暂存变更。
3. 使用 **Sparkle** 按钮生成提交消息。

   ![Source Control 面板显示 Copilot 所做的变更](../../../_images/ex3-source-control-changes.png)

4. 选择 **Publish**，将分支推送到存储库。

## 创建拉取请求

有多种创建拉取请求的方式，包括通过 github.com 和 GitHub 命令行界面 (CLI) 创建。不过既然你正在使用 GitHub Copilot，不如让它代为创建 PR！它可以找到相关 issue 并创建关联到该 issue 的 PR。

1. 导航到 Copilot Chat 面板，选择 **New Chat** 开始新会话。
2. 确认智能体下拉列表中选择了 **Agent**，以便 Copilot 使用 GitHub 工具。
3. 请 Copilot 创建 PR：

   ```plaintext
   Find the issue in the repo related to filtering by category and publisher. Create a new pull request for the current branch, and associate it with the correct issue.
   ```

4. 根据需要选择 **Continue**，允许 Copilot 执行收集信息和操作所需的任务。首次使用 GitHub 工具时，系统可能会要求你登录 GitHub；按照提示完成登录。
5. 留意 Copilot 如何搜索 issue、找到正确 issue 并创建 PR。
6. 选择 Copilot 生成的链接审查拉取请求，但**暂时不要合并**。

## 总结和后续步骤

恭喜！本练习中，你：

- 了解了 Model Context Protocol (MCP) 以及 MCP 服务器如何使用新工具扩展 Copilot。
- 将 Playwright MCP 服务器添加到工作区。
- 在发布前使用它通过浏览器手动测试筛选功能。
- 发布了分支，并为筛选工作创建了拉取请求。

你使用 MCP 服务器来**测试**功能，但 MCP 只是扩展 Copilot 的方式之一。接下来，[创建自定义智能体][next-lesson]，简化无障碍审查等专注任务。

## 资源

- [MCP 到底是什么，为什么大家都在讨论？][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [GitHub MCP Server][github-mcp-server]
- [GitHub MCP 注册表][mcp-registry]
- [VS Code 中的 MCP 服务器][vscode-mcp-config]

[previous-lesson]: ../2-agent-mode/
[next-lesson]: ../4-custom-agents/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[github-mcp-server]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[remote-github-mcp-server]: https://github.blog/changelog/2025-06-12-remote-github-mcp-server-is-now-available-in-public-preview/
[vscode-mcp-config]: https://code.visualstudio.com/docs/agents/reference/mcp-configuration
