---
title: "レッスン 10 - 振り返りと次のステップ"
description: "Copilot CLI のワークフロー、2つのプルリクエスト、再利用可能なカスタマイズ、追加のリソースを振り返ります。"
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

GitHub Copilot CLI を使い、Tailspin Toys の一連の開発作業に取り組みました。次のことを行いました。

- Codespace を準備し、Copilot CLI をインストールしてプロジェクトを確認し、登録済みのフィルタリング issue を見つけました。
- 星評価を追加し、転送されたブラウザーで結果を確認して、最初のプルリクエスト (PR) を手動でマージしました。
- フィルタリングの issue から始め、Plan モードで方針を決め、Autopilot モードで実装し、Interactive モードでレビューしました。
- カスタム命令でエージェントを導き、既存の `quality-checks` スキルをカスタマイズして、ユニット テスト、lint、型チェックを実行しました。
- Playwright Model Context Protocol (MCP) サーバーを追加し、実際のブラウザーでフィルタリングを確認しました。
- 品質保証 (QA) カスタム エージェントを作成して選択し、要件、カバレッジ、スキルの結果、ブラウザーでの根拠を評価しました。
- フィルタリングの変更全体をレビューし、フィルタリング PR に対して Agent Merge を許可しました。
- コンテキスト、モデル、共有、任意のクラウドへの委任に使う slash command を確認しました。

## 作成した機能

このワークショップには、それぞれ更新済みの `main` から作成した別のブランチで、2つの PR を作成する節目があります。

1. **星評価**: 既存の `starRating` と未評価の状態をゲームカードに表示します。
2. **フィルタリングと品質ワークフロー**: フィルタリングを実装し、命令を更新して機能に適用し、`quality-checks` のレポートをカスタマイズして QA プロファイルを作成し、関連するテストを追加します。

フィルタリングを計画して PR を開くまでは、同じ会話とブランチを使いました。ワークショップを簡潔にするため、作業を1つの PR にまとめました。

## さまざまな検証方法

自動テスト、自分で行うブラウザー上の確認、MCP を介した Copilot のブラウザー操作など、複数の方法でコードを確認しました。`quality-checks` スキルはユニット テスト、lint、型チェックを実行し、新しい形式で報告しました。QA はこれらの結果に要件とテスト カバレッジのレビューをまとめて、PR の前に確認しました。

追加するテストは、実際の不足を補うものであるべきです。新たなテストが必要ない QA の結果が正しい場合もあります。マージを許可する前に、コードと根拠を確認し、変更後は関連する根拠を更新してください。

## ベスト プラクティス

Copilot に与えるコンテキストとツールが、作業内容を左右します。このワークショップでは、命令を更新し、スキルをカスタマイズし、QA プロファイルを作成し、MCP サーバーを設定しました。会話をまたいでカスタマイズを再利用し、チームの要望に合わせて調整しましょう。命令は標準を定め、スキルは繰り返し行う作業を記述し、カスタム エージェントは専門的な役割を定義し、MCP サーバーは外部ツールに接続します。エージェントの要約だけでなく、実際の変更とツールの結果を確認してください。

タスクに合わせて**モードとモデル**を選びます。構築前に方針を検討するには **Plan**、範囲を絞った変更に自分も関わるには **Interactive**、明確な範囲のタスクには **Autopilot** を使います。定型的な編集には高速なモデルを、複雑な作業にはより高性能なモデルを選びます。

インフラストラクチャと同じくらい、コンテキストも重要です。何を、なぜ、どのように作るかを明確に説明すると、結果が大きく変わります。

## さらに試す

主要なワークフローを学びました。次は、Copilot CLI のほかの機能も試してみましょう。

- `/review` で、コード レビュー エージェントに変更を分析してもらいます。
- `/rubber-duck` で、問題について対話し、別の視点を得ます。
- `/fleet` で、独立したサブタスクを並行して調整します。
- `/worktree` で、別のタスクを分離します。
- `/delegate` で、Copilot cloud agent にタスクを送信します。

## 次のステップ

どのツールも、使い続けることが上達につながります。製品コード、趣味のコード、長い間作りたかった小さなアプリなどで活用しましょう。チームと学んだことを共有し、互いに学び合ってください。ドキュメントも引き続き確認しましょう。

ターミナルから Tailspin Toys を拡張する場合は、任意の[Foundry Backer Concierge シリーズ][foundry]に進みます。ほかの環境での作業を比べるには、[VS Code ワークショップ][vscode]、[GitHub Copilot app ワークショップ][app]、[Copilot cloud agent ワークショップ][cloud]を確認してください。

## リソース

- [GitHub Copilot CLI について][about-cli]
- [Copilot CLI コマンド リファレンス][cli-reference]
- [Copilot CLI をカスタマイズする][customize-cli]
- [Copilot CLI でプルリクエストを管理する][manage-prs]

[previous-lesson]: ../9-cli-power-tools/
[foundry]: ../8-foundry-agent/
[vscode]: ../../vscode/
[app]: ../../app/
[cloud]: ../../cloud/
[about-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[customize-cli]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
