---
title: "练习 1 - 自定义指令（VS Code）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：智能体模式 →][next-lesson]

使用生成式 AI 时，上下文至关重要。如果任务必须以特定方式完成，或者 Copilot 应了解一些背景信息，就需要确保它能获取这些上下文。你可以通过多种方式向 Copilot 提供特定上下文，其中一种重要方式是使用[指令文件][instruction-files]，为代码生成提供指导。

本练习将学习如何：

- 通过存储库自定义指令和按路径限定范围的指令文件，让 Copilot 获取项目上下文、编码指南和文档标准。
- 在现有指令下生成筛选功能的第一个数据切片（发行商辅助函数）。
- 在 `.github/copilot-instructions.md` 中添加新的存储库级标准。
- 再次发送相同提示，观察生成的代码如何采用新标准。
- 将指令更新和筛选切片提交到 `main`，以便 Copilot 在下一练习中使用更新后的指导。

> [!CAUTION]
> 生成的代码可能不符合你设定的部分标准。Copilot 具有非确定性。本练习的目标是观察更新指令后行为的*趋势*，而不是要求输出逐字一致。

## 指令文件

### 场景

作为一家优秀的软件开发公司，Tailspin Toys 制定了一些开发实践准则和要求，包括：

- 数据层始终需要单元测试。
- UI 应采用暗色模式并具有现代感。
- 应通过 TSDoc 文档注释为代码添加文档。
- 每个文件开头都应添加一段注释，说明文件的用途。

通过使用指令文件，你可以确保 Copilot 掌握正确的信息，以符合上述实践要求完成任务。

### 自定义指令

自定义指令可以向 Copilot 提供上下文和偏好，帮助它了解你的编码风格和要求。你可以借此引导 Copilot 提供更符合需求的建议和代码片段。你可以为整个存储库创建指令，也可以针对特定类型的文件创建任务级指令。

指令文件有两种：

- `.github/copilot-instructions.md` 是一个指令文件，会随针对该存储库的**每个**请求发送给 Copilot。此文件应包含项目级信息——与发送给 Copilot 的大多数聊天或 CLI 请求相关的上下文，例如技术栈、项目概述、最佳实践和全局指导。
- `.github/instructions/*.instructions.md` 文件可针对特定任务或文件类型创建。你可以用它们提供特定语言（如 TypeScript 或 Astro）的指导，也可以用于创建 UI 组件或新的单元测试等任务。

> [!NOTE]
> 在 IDE 中，指令文件仅用于 Copilot Chat 的代码生成，不用于代码补全或下一步编辑建议。
>
> Copilot Chat、Copilot CLI 和 Copilot 云智能体在生成代码时都会使用存储库级指令和带有 `applyTo` frontmatter 的 `*.instructions.md` 文件。
>
> Copilot [还支持其他标准的指令文件][custom-instructions-support]，包括 AGENTS.md 和 CLAUDE.md 文件。

### 管理指令文件的最佳实践

全面讨论如何创建指令文件超出了本工作坊的范围。不过，示例项目展示了具有代表性的方法。总体而言：

- 将 `copilot-instructions.md` 中的内容限定为项目级指导，例如项目说明、项目结构和全局编码标准。
- 使用 `*.instructions.md` 文件提供针对特定文件类型（单元测试、Astro 组件、数据层）或特定任务的指导。
- 使用自然语言。指导应清晰，并提供代码应如何编写及不应如何编写的示例。

创建指令文件没有唯一的方法，使用 AI 也是如此。通过尝试，你会找到适合自己项目的方法。

> [!TIP]
> 每个使用 GitHub Copilot 的项目都应有一套完善的指令文件。浏览本项目的文件时，你可能会发现有针对多种任务的指令，包括 [UI 更新][ui-instructions]和 [Astro][astro-instructions]。
>
> Copilot 也可以帮你生成指令文件。不同产品界面的操作方式有所不同（例如 VS Code 中的 **Configure Chat → Generate Agent Instructions**，或 Copilot CLI 中的 `/init`）；相关练习会在适当时介绍。
>
> 想要模板或起点？可以查看 [awesome-copilot][awesome-copilot]，其中包含许多指令文件、自定义智能体和其他资源。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## 探索本项目中的自定义指令文件

花点时间阅读存储库自带的指令文件——包括核心文件 `copilot-instructions.md`，以及针对不同任务的 `*.instructions.md` 文件。在编辑器或 GitHub 网页界面中打开这些文件。

1. 打开 `.github/copilot-instructions.md`。
2. 浏览文件，留意项目简介以及 **Agent notes**、**Code standards**、**Scripts** 和 **Repository Structure** 等部分。注意其中的 **Code standards** 还包含 **GitHub Actions Workflows** 子部分。这些内容适用于你与 Copilot 的所有交互。
3. 打开 `.github/instructions` 文件夹并查看其中的文件。这里有针对 Astro 文件、Drizzle 数据层、测试等内容的指令。
4. 打开 `.github/instructions/unit-tests.instructions.md`。留意顶部的 `applyTo` 字段，它使用相对于存储库根目录的 glob 指定指令适用的文件。此处，任何 TypeScript 测试文件（例如匹配 `**/*.test.ts` 的文件）都符合条件。
5. 留意针对本项目单元测试的具体指令。
6. 最后，打开 `.github/instructions/drizzle.instructions.md` 并滚动到文件底部。留意其中指向其他指令文件（如 `unit-tests.instructions.md`）和项目现有文件的链接。这样可以将较大的指令集拆分为可复用的小文件，并在生成代码时提供示例供 Copilot 参考。（其中的路径相对于指令文件，而不是存储库根目录。）

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分介绍了项目的编码标准，但尚未要求添加代码文档。下一步将添加 TSDoc 文档注释和文件级注释头的规则。

## 为变更创建分支

现在遵循最佳实践，为变更创建分支。

1. 返回上一练习中使用的 Codespace。
2. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux），打开新终端。
3. 创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions
   ```

## 更新指令前先使用 Copilot Chat

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 Codespace，打开 Copilot Chat 面板，然后选择 **New Chat** 开始干净的对话。不同练习所用的模式和模型可能不同；每一步会在需要时说明。

要了解自定义指令的效果，先使用当前的指令文件发送提示。之后再更新指令并重新发送同一提示，比较两次的结果。

1. 关闭之前练习中打开的编辑器标签页，让 Copilot 只获取你需要的上下文。
2. 打开 `src/lib/publishers.ts`，让 Copilot 知道辅助函数应该放在哪里。
3. 在 Chat 视图的智能体下拉列表中选择 **Agent**，让 Copilot 可以应用文件变更。

   ![聊天视图中显示智能体选择器的屏幕截图。](../../../_images/shared-chat-mode-selector.png)

4. 发送以下提示：

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot 会探索项目并应用代码更新，通常会同时修改辅助函数文件和测试。
6. 留意建议的辅助函数是一个有类型的函数，它将 `db` 客户端作为第一个参数，并返回有类型的发行商数组——这是 `.github/instructions/drizzle.instructions.md`（适用于 `src/lib/*.ts`）中的数据层约定发挥作用。
7. 留意建议的代码**缺少** TSDoc 文档注释和文件级注释头。

> [!CAUTION]
> Copilot 具有概率性，因此它可能会在没有明确指示的情况下添加文档注释。如果发生这种情况也没关系——本练习的重点是观察更新指令后的行为是否更加一致。

## 添加新的存储库标准

如前所述，`.github/copilot-instructions.md` 用于向 Copilot 提供项目级信息。现在添加一条编码标准，以改善代码建议。

1. 重新打开 `.github/copilot-instructions.md`。
2. 找到 **Code formatting requirements** 部分，通常在第 27 行附近。注意该部分描述了项目的编码标准，但还没有代码文档规则，这正是生成的辅助函数缺少文档注释的原因。
3. 在现有标准下方添加以下 Markdown 内容，要求 Copilot 添加文件注释头和 TSDoc 文档注释：

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. 保存 `copilot-instructions.md`。

> [!TIP]
> 如上一课所示，指令文件可以放在存储库级的 `.github/copilot-instructions.md` 中，为全局提供指导；也可以使用针对特定语言、文件类型或任务的 `*.instructions.md` 文件。存储库级文件适合放置诸如文档注释要求这样的项目标准。

## 重新发送提示并观察变化

1. 返回 Copilot Chat 并选择 **New Chat**，清除对话内容。
2. 再次打开 `src/lib/publishers.ts`，让 Copilot 专注于正确的文件。
3. 发送与之前相同的提示：

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 留意建议的文件现在会以类似以下注释块开头：

   ```typescript
   /**
    * Tailspin Toys 众筹平台的发行商数据访问辅助函数。
    * 提供从数据库检索发行商信息的函数。
    */
   ```

5. 留意建议的函数现在包含类似以下内容的 TSDoc 注释：

   ```typescript
   /**
    * 返回所有发行商的 ID 和名称列表。
    *
    * @param db - Drizzle 数据库客户端。
    * @returns 一个 Promise，解析为发行商对象数组。
    */
   ```

你刚才引导 Copilot 遵循新的项目标准，并将其应用于真实代码。下一练习会基于这些代码继续开发。

## 提交指令并推送分支

指令文件和其他资源一样，也需要使用源代码管理。现在提交并推送分支。

1. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux），打开新终端。
2. 在终端中运行以下命令，确认指令更新和辅助函数变更已存在：

   ```bash
   git status
   ```

3. 在终端中暂存并提交指令更新，以及 Copilot 为辅助函数基础结构修改的所有文件：

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # 如果 git status 显示还有其他配套变更（例如测试），也要添加这些文件。
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. 将分支推送到存储库：

   ```bash
   git push -u origin custom-instructions
   ```

## 创建并合并拉取请求

分支已推送，现在应创建拉取请求，并关联到待办列表中的文档标准 issue。可以手动完成，也可以提示 Copilot 查找 issue、创建用于关闭该 issue 的 PR，然后合并。

> [!NOTE]
> 这是第一次让 Copilot 代表你操作 GitHub。项目模板已配置好 GitHub 工具，因此无需设置——Copilot 首次使用其中一个工具时，VS Code 可能会提示你登录 GitHub。按照提示授权即可。后续练习会介绍这种连接如何工作（Model Context Protocol）。

1. 在 Codespace 中打开 Copilot Chat。
2. 按 <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd>（Windows/Linux）打开 Copilot Chat 视图，并确认在智能体选择器中选择了 **Agent**。
3. 请 Copilot 查找与更新指令文件相关的 issue，并从当前分支创建 PR，说明指令更新和新的发行商辅助函数基础结构：

   ```
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot 提示时，选择 **Allow**，允许它代表你执行 GitHub 操作。请先审查命令，再决定是否允许。
5. PR 创建后，请 Copilot 合并 PR 并将分支切回 `main`，可以使用以下提示：

   ```
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

6. 如果系统提示允许 Copilot 代表你执行 GitHub 操作和运行 shell 命令，请先审查命令，再决定是否允许。

你已借助 GitHub Copilot 创建并合并了拉取请求！

## 总结和后续步骤

你已了解 Copilot 如何获取本项目的指令文件，并使用 VS Code 中的 Copilot Chat：

- 发送代码生成提示，观察 Copilot 在*现有*指令下生成的结果。
- 在 `.github/copilot-instructions.md` 中添加新的存储库级标准。
- 再次发送相同提示，观察建议的代码如何采用新标准。
- 将指令更新和辅助函数基础结构提交到 `main`，以便下一练习继续使用。

接下来，在[智能体模式][next-lesson]中应用这些指令，让 Copilot 跨代码库添加新功能。

## 资源

- [用于自定义 GitHub Copilot 的指令文件][instruction-files]
- [创建自定义指令的最佳实践][instructions-best-practices]
- [编写更好的 Copilot 自定义指令的 5 个技巧][copilot-instructions-five-tips]
- [GitHub Copilot 个人自定义指令][personal-instructions]
- [Awesome Copilot — 指令文件和其他资源集合][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
