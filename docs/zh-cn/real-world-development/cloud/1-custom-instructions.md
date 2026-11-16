---
title: "练习 1 - 自定义指令（云智能体）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：Copilot 云智能体 →][next-lesson]

使用生成式 AI 时，上下文至关重要。如果任务必须以特定方式完成，或者 Copilot 应了解一些背景信息，就需要确保它能获取这些上下文。[指令文件][instruction-files]可以提供指导，让 Copilot 不仅理解你想让它做什么，也知道应该如何完成。

本练习将学习如何：

- 通过存储库自定义指令和按路径限定范围的指令文件，让 Copilot 获取项目上下文、编码指南和文档标准。
- 在 `.github/copilot-instructions.md` 中添加新的存储库级标准。

> [!NOTE]
> 与 VS Code 和 CLI 练习不同，这里不会运行“更新前/更新后”的提示。Copilot 云智能体会在 GitHub issue 上异步工作，因此难以实时并排展示指令的影响。稍后审查云智能体创建的拉取请求时，你会看到指令文件带来的影响。

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

## 添加新的存储库标准

接下来要编辑一次：添加一条项目级规则，要求以 TSDoc 文档注释和文件级注释头的形式在代码中编写文档。后续练习中，云智能体会在处理你分配的 issue 时遵循此要求。

编辑前先创建一个分支（云智能体会从 issue 目标分支读取指令，但你仍应整洁地提交自己的修改）：

1. 在 Codespace 终端中创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions-cloud
   ```

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

## 提交、推送并合并指令更新

云智能体会读取 issue 目标分支中的指令。在下一练习中将 issue 分配给 Copilot 时，它会从 `main` 分支创建分支。因此，必须先将指令变更合并到 `main`，云智能体才能使用。

1. 暂存并提交：

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. 推送分支：

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. 在 github.com 上创建从 `custom-instructions-cloud` 到 `main` 的拉取请求并将其合并。这样，云智能体在下一练习中处理 issue 时就会读取这些指令。

> [!TIP]
> 如果希望在本工作坊中直接使用 `main`，可以跳过分支步骤并直接提交到 `main`。这里保留分支步骤是为了模拟真实项目中的工作方式。

## 总结和后续步骤

你已了解 Copilot 如何获取本项目的指令文件，并向 `.github/copilot-instructions.md` 添加了一项存储库级标准。后续练习中，你会看到云智能体创建的拉取请求如何遵循此标准。

接下来，[将第一个 issue 分配给 Copilot 云智能体][next-lesson]。

## 资源

- [用于自定义 GitHub Copilot 的指令文件][instruction-files]
- [创建自定义指令的最佳实践][instructions-best-practices]
- [编写更好的 Copilot 自定义指令的 5 个技巧][copilot-instructions-five-tips]
- [Awesome Copilot — 指令文件和其他资源集合][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
