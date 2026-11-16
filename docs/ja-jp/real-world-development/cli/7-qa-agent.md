---
title: "レッスン 7 - QA エージェントを作成して使う"
description: "issue の要件、quality-checks スキル、Playwright MCP を組み合わせた QA カスタム エージェントを作成します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

`quality-checks` スキルを使った自動チェックと、Playwright MCP を使ったブラウザーでのフィルタリング体験の確認を行いました。次は、これらの機能を明確な QA プロセスを持つカスタム エージェントにまとめます。

このレッスンでは、次のことを行います。

- カスタム エージェントが命令、スキル、MCP ツールと連携する方法を確認します。
- 再利用できる品質保証プロファイルを作成して調べます。
- QA エージェントを選択し、フィルタリングの issue に照らして結果をレビューします。

## シナリオ

Tailspin Toys では、プルリクエスト (PR) を作成する前に、要件、コード品質、自動チェック、テスト カバレッジ、ブラウザー上の動作を一貫して確認したいと考えています。カスタム エージェントを使うと、QA プロセスを調整し、再利用可能なレポートを作成できます。

## カスタム エージェントとは

カスタム エージェントは、Markdown プロファイルで定義する、Copilot の特化したバージョンです。プロファイルには、エージェントの目的、命令、利用可能なツールを記述します。このワークショップでは、`.github/agents/qa.agent.md` に QA の役割を定義し、Copilot CLI で選択します。

これまで使用したカスタマイズには、それぞれ異なる役割があります。リポジトリの命令はチームの標準を説明します。`quality-checks` スキルは繰り返し実行できるチェックをまとめます。Playwright MCP はブラウザー ツールを提供します。QA プロファイルは、要件を評価して結果を報告するために、Copilot がこれらの機能をどう使うかを示します。既存の機能を置き換えるものではなく、別の会話も必要ありません。

## QA プロファイルを作成する

機能の PR を開く前に、再利用できる QA プロファイルの作成を Copilot に依頼します。プロファイルでは、QA のチェック内容と守るべき範囲の両方を定めます。

1. Codespace に戻り、フィルタリングの会話が Interactive モードであることを確認します。
2. デフォルト エージェントに、次のカスタム エージェントの作成を依頼します。

   ```plaintext
   Create a custom agent named QA in .github/agents/qa.agent.md. It should check features against their issues and agreed requirements, follow the repository instructions, run the quality-checks skill, use Playwright MCP to verify behavior, and add tests when coverage is missing.

   Have it report each requirement as pass, fail, or blocked with supporting evidence. It must ask before changing implementation code, and it must not commit changes or open pull requests.

   Just create the profile for now so I can review it.
   ```

## プロファイルを確認する

新しいエージェントを使う前に、意図した QA の手順と制約が反映されているか、プロファイルをレビューします。これにより、確認だけを求めたのにエージェントが不完全な設定や広すぎる権限で機能を変更する事態を防げます。

1. `/diff` を入力し、`.github/agents/qa.agent.md` を開きます。
2. frontmatter を読みます。`description` は必須です。`name` は任意ですが、指定するとエージェントの表示名が明確になります。
3. プロファイルの命令を読み、QA が要件を起点にし、リポジトリの命令に従い、`quality-checks` スキルを実行し、Playwright MCP を使うことを確認します。
4. QA が根拠を示して報告し、実装コードを変更する前に確認を求め、変更をコミットしたり PR を開いたりしないことを確認します。
5. いずれかの役割や制約が不足していたら、続ける前にデフォルト エージェントに修正を依頼します。

## issue に対して QA を実行する

Copilot CLI は会話の開始時にプロジェクト エージェントを読み込みます。プロファイルを作成したら同じフィルタリング会話を再開し、QA を選択します。そうすれば、issue と計画時の決定事項をそのまま利用できます。

1. 次のプロンプトでエージェントを有効にします。

   ```plaintext
   /agent QA
   ```

> [!NOTE]
> 作成したばかりのエージェントは、利用可能なエージェント一覧に表示されない場合があります。上記のコマンドを実行すれば有効になります。

2. 次のプロンプトで、QA エージェントに機能をレビューするよう依頼します。

   ```plaintext
   Review the filtering feature against the issue and the decisions in our plan. Is it ready for a PR?
   ```

3. QA エージェントが作業を始めます。
4. 完了したら、レポートを読みます。

## まとめと次のステップ

再利用可能な専門的役割を追加し、その成果をレビューしました。このレッスンでは、次のことを行いました。

- カスタム エージェントが命令、スキル、MCP ツールと連携する方法を確認しました。
- 品質保証プロファイルを作成して調べました。
- QA エージェントを選択し、フィルタリングの issue に照らして結果をレビューしました。

実装、スキルの更新、QA プロファイル、テスト、検証レポートの準備ができました。次は[機能 PR にまとめ、Agent Merge を使い][next-lesson]ます。

## リソース

- [Copilot CLI 用のカスタム エージェントを作成する][create-agents]
- [カスタム エージェントの構成][agent-config]

[previous-lesson]: ../6-mcp-playwright/
[next-lesson]: ../8-create-pull-request/
[create-agents]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli
[agent-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
