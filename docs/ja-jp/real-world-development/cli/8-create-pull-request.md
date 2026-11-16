---
title: "レッスン 8 - 機能 PR を作成してマージする"
description: "フィルタリング、命令、スキルの更新、QA プロファイル、テストをまとめてレビューし、PR を作成して Agent Merge を使います。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

フィルタリングの実装、命令の更新、スキルの更新、品質保証 (QA) プロファイル、テストは、すべて一つのブランチに保存されています。まとめてレビューし、プルリクエストを開きましょう。星評価のプルリクエスト (PR) は自分でマージしましたが、今回は **Agent Merge** にそのプロセスを任せます。

> [!NOTE]
> 通常であれば、機能、命令の更新、スキルの更新、QA エージェントを複数の PR に分けます。ワークショップを簡潔にするため、フィルタリングと品質ワークフロー全体を一つの会話とブランチで進め、この PR にまとめました。

このレッスンでは、次のことを行います。

- Agent Merge と、マージ ライフサイクルを自動化する方法を学びます。
- 機能全体の変更と検証の根拠を調べます。
- フィルタリングの PR を作成します。
- レビュー後に Agent Merge を有効にし、PR がマージされたことを確認します。

## シナリオ

フィルタリングの開発を通じて、Copilot を使って機能を計画、実装、検証しました。Tailspin Toys では、マージの許可を開発者が管理しながら、残りの PR 作業を自動化したいと考えています。

## Agent Merge について

**Agent Merge** は、プルリクエストをマージ可能な状態にするための残りの作業を自動化します。有効にすると、Copilot は PR を妨げている問題に対処します。たとえば、失敗した継続的インテグレーション (CI) チェックの修正、レビュー コメントへの対応、必要に応じた rebase を行い、リポジトリで許可されていれば GitHub の自動マージを有効にします。

これまでは自分で **Merge pull request** を選択していました。Agent Merge にその役割を任せることもできます。有効化する前に作業をレビューし、マージしてよいと判断してください。

## Agent Merge で PR を管理する

コードがそろったら、全体をレビューし、PR を作成して、残りの手順を Agent Merge に任せましょう。

1. Codespace に戻ります。
2. `/agent` を入力してエージェント ダイアログを開きます。
3. 一覧から **Default** を選び、<kbd>Enter</kbd> を押します。
4. `/pr create` コマンドで新しい PR を作成します。
5. `/pr agentmerge` を実行して Agent Merge を有効にします。
6. Copilot が PR の継続的インテグレーションを監視します。すべて成功すると、マージが実行されます。
7. 「PR #14 was squash-merged successfully.」のような Copilot のメッセージが表示されたことを確認します。

> [!IMPORTANT]
> Agent Merge は、必須の承認、ブランチ保護、マージ キュー、リポジトリの設定、権限不足を回避しません。処理がブロックされた場合は報告された理由を確認し、リポジトリで許可されている場合は、レビュー済みの変更を手動でマージしてください。

## まとめと次のステップ

コードの生成、テストと検証、プルリクエストのプロセスなど、開発工程の一部を自動化しました。このレッスンでは、次のことを行いました。

- Agent Merge と、マージ ライフサイクルを自動化する方法を学びました。
- 機能全体の変更と検証の根拠を調べました。
- フィルタリングの PR を作成しました。
- レビュー後に Agent Merge を有効にし、PR がマージされたことを確認しました。

次は、コンテキスト、モデル、共有、任意のクラウドへの委任に使う[便利な Copilot CLI の slash command][next-lesson]を調べます。

## リソース

- [Copilot CLI でプルリクエストを管理する][manage-prs]
- [Copilot CLI コマンド リファレンス][cli-reference]

[previous-lesson]: ../7-qa-agent/
[next-lesson]: ../9-cli-power-tools/
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
