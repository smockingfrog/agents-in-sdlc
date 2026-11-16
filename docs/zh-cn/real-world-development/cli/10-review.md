---
title: "第 10 课 - 回顾和后续步骤"
description: "回顾 Copilot CLI 工作流、两个拉取请求、可复用的自定义功能和更多资源。"
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

你已在 Tailspin Toys 的完整工作流中使用 GitHub Copilot CLI。你：

- 准备了 Codespace、安装了 Copilot CLI、探索了项目，并找到了预置的筛选 issue。
- 添加了星级评分，在转发的浏览器中审查结果，并手动合并了第一个拉取请求 (PR)。
- 从筛选 issue 开始，在 Plan 模式下确定方案，在 Autopilot 模式下实现，并在 Interactive 模式下审查。
- 使用自定义指令引导智能体，自定义现有的 `quality-checks` 技能，并使用它运行单元测试、lint 和类型检查。
- 添加了 Playwright Model Context Protocol (MCP) 服务器，并用它在真实浏览器中探索筛选功能。
- 创建并选择质量保证 (QA) 自定义智能体，以评估要求、覆盖率、技能结果和浏览器证据。
- 审查了完整的筛选变更，并授权 Agent Merge 处理筛选 PR。
- 探索了用于管理上下文、模型、共享和可选云端委派的斜杠命令。

## 交付的内容

本工作坊有两个 PR 阶段，每个阶段都从更新后的 `main` 分支创建独立分支：

1. **星级评分：**在游戏卡片上显示现有的 `starRating`，并明确显示未评分状态。
2. **筛选和质量工作流：**实现筛选、更新指令并将其应用到功能、自定义 `quality-checks` 报告、创建 QA 配置，并加入相关测试。

从规划筛选功能到创建 PR，你一直使用同一个对话和分支。为了简化工作坊流程，这些工作合并到了一个 PR 中。

## 不同类型的验证

你通过多种方式检查代码：自动化测试、自己在浏览器中检查，以及通过 MCP 使用 Copilot 探索浏览器。`quality-checks` 技能运行了单元测试、lint 和类型检查，并按新格式报告结果。QA 将这些结果与要求审查和测试覆盖率检查汇总在一起，然后再创建 PR。

新增的测试应弥补真实存在的缺口；QA 运行后无需添加测试也可能是正确结果。授权合并前，请检查代码和证据；如果代码有变更，应重新检查相关证据。

## 最佳实践

提供给 Copilot 的上下文和工具会影响它的工作。在本工作坊中，你更新了指令、自定义了技能、创建了 QA 配置并配置了 MCP 服务器。可以在不同对话中复用这些自定义功能，并根据团队需求调整。指令规定标准，技能描述可重复执行的任务，自定义智能体定义专用角色，MCP 服务器连接外部工具。请检查实际变更和工具结果，而不只是智能体的摘要。

根据任务选择合适的**模式和模型**。使用 **Plan** 在构建前考虑方案；使用 **Interactive** 参与专注范围内的变更；对范围明确的任务使用 **Autopilot**。常规编辑可选择更快的模型，复杂任务则可选择能力更强的模型。

上下文与基础设施同样重要。明确描述要构建的内容、原因和方式，会显著影响输出。

## 继续探索

你已完成核心工作流。还可以了解一些 Copilot CLI 功能：

- `/review`：请代码审查智能体分析变更。
- `/rubber-duck`：讨论问题并获取另一种观点。
- `/fleet`：并行协调独立子任务。
- `/worktree`：隔离单独的任务。
- `/delegate`：将任务交给 Copilot 云智能体。

## 后续步骤

熟练使用工具的最佳方式就是持续使用它！在生产代码、个人项目中使用它，也可以用它构建那个你多年来一直想做却没时间动手的小应用。与团队分享经验，也向他们学习。和往常一样，别忘了查阅文档。

如果想继续从终端扩展 Tailspin Toys，可以接着学习可选的 [Foundry Backer Concierge 系列][foundry]。若要比较其他环境，可以探索 [VS Code 工作坊][vscode]、[GitHub Copilot 应用工作坊][app]或 [Copilot 云智能体工作坊][cloud]。

## 资源

- [关于 GitHub Copilot CLI][about-cli]
- [Copilot CLI 命令参考][cli-reference]
- [自定义 Copilot CLI][customize-cli]
- [使用 Copilot CLI 管理拉取请求][manage-prs]

[previous-lesson]: ../9-cli-power-tools/
[foundry]: ../8-foundry-agent/
[vscode]: ../../vscode/
[app]: ../../app/
[cloud]: ../../cloud/
[about-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[customize-cli]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
