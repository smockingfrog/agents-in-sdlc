---
title: "レッスン 9 - GitHub Copilot CLI の slash command"
description: "コンテキスト管理、モデル選択、セッション共有、任意のクラウドへの委任に使う slash command を確認します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

ほかの CLI ツールと同様、GitHub Copilot CLI には対話に使える slash command が多数あります。高度な機能、内部情報、追加の構成オプションを利用できます。これまでに `/diff`、`/mcp`、`/skills`、`/agent`、`/pr` などのコマンドを使いました。次は、ほかの便利なコマンドをいくつか見ていきましょう。

このレッスンでは、次のことを行います。

- `/context` と `/compact` で、Copilot が会話のコンテキストをどう管理するか確認します。
- `/model` で、利用可能なモデルを確認します。
- `/share` でセッションをエクスポートまたは共有する方法を学びます。
- 並列作業、worktree、cloud agent への委任に使う任意のコマンドを確認します。

## シナリオ

基本的な CLI ワークフローが完了しました。次は、コンテキストの管理、モデルの切り替え、セッションの共有、[Copilot cloud agent][about-cloud-agent]への任意の委任など、追加機能を確認します。

## Copilot CLI のコンテキストを調べる

大規模または複雑なタスクでは、モデルの最大コンテキスト ウィンドウに達することがあります。Copilot CLI は必要に応じて会話を自動的に圧縮します。また、slash command を使って状態を確認したり、自分で圧縮したりできます。

1. Codespace に戻り、まだ起動していない場合はリポジトリ ルートから Copilot CLI を起動します。
2. 次のコマンドを入力します。

   ```plaintext
   /context
   ```

3. モデル、現在のトークン使用量、システム命令、ツール、メッセージ、空き領域へのコンテキストの配分を確認します。
4. 会話を圧縮します。

   ```plaintext
   /compact
   ```

5. もう一度 `/context` を入力し、結果を比較します。会話が短い場合は、大きな違いが見られないことがあります。

> [!NOTE]
> コンテキスト ウィンドウの空きが少なくなると、Copilot CLI は自動的にコンテキストを圧縮します。自分でタイミングを決める場合は `/compact` を使います。関連のない別タスクを始める場合は、`/clear` または `/new` で会話をリセットします。

## モデルを選ぶ

モデルごとに得意分野が異なり、開発者によって好みも違います。Copilot CLI では、使用可能なモデルの一覧表示と選択ができます。

1. 次のコマンドを入力します。

   ```plaintext
   /model
   ```

2. 使用可能なモデルと使用状況に関する情報を確認します。
3. 現在のモデルをそのまま使うか、別のモデルを選ぶか、<kbd>Esc</kbd> を押して一覧を閉じます。

## セッションを共有する

チームで学んだことを共有すると、AI ツールをより効果的に活用できます。`/share` コマンドを使うと、セッションを Markdown または HTML ファイルにエクスポートしたり、共有リンクを作成したり、GitHub gist を公開したりできます。

1. `/help` を入力し、インストール済みのバージョンで利用できる `/share` のオプションを確認します。
2. セッションを共有する場合は、ローカルに Markdown を出力する `/share file` など、目的に合う方法を選びます。
3. エクスポートした内容を誰かに送ったり公開したりする前にレビューします。セッションのエクスポートには、プロンプト、応答、プロジェクトの詳細が含まれる場合があります。

リンクや gist の公開は任意です。チームが共有を意図していないリポジトリや会話の内容は公開しないでください。

## 任意: 作業の拡張や委任

ワークショップの主要な内容は完了しました。Copilot CLI には、大きなタスクに役立つコマンドもあります。

- `/fleet` は、独立したサブタスクを複数の subagent に割り当て、並行して実行できます。
- `/worktree` は、別のタスク用に分離された Git worktree を作成できます。
- `/delegate` は、非同期で動作し、プルリクエストを開くこともある Copilot cloud agent にタスクを送信できます。

これらのコマンドを使うと、worktree が追加で作成されたり、リモートで作業が行われたりするため、任意の内容です。試すときは、新しい範囲の明確なタスクから始め、通常のワークフローで結果をレビューしてください。非同期エージェントの作業を詳しく学ぶ場合は、[cloud agent ワークショップ][cloud-workshop]に進んでください。

## まとめと次のステップ

Copilot CLI の slash command を使うと、設定を変更したり、セッションを共有したり、内部の動作を確認したりできます。このレッスンでは、次のことを行いました。

- `/context` と `/compact` で、Copilot が会話のコンテキストをどう管理するか確認しました。
- `/model` で、利用可能なモデルを確認しました。
- `/share` でセッションをエクスポートまたは共有する方法を学びました。
- 並列作業、worktree、cloud agent への委任に使う任意のコマンドを確認しました。

Copilot CLI には、ほかにも多くの slash command があります。最後に、これまでに学んだことと、今後の学習方法を[振り返りましょう][next-lesson]。

## リソース

- [Copilot CLI コマンド リファレンス][cli-reference]
- [Copilot CLI のコンテキスト管理][context-management]
- [Copilot cloud agent について][about-cloud-agent]

[previous-lesson]: ../8-create-pull-request/
[next-lesson]: ../10-review/
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[context-management]: https://docs.github.com/copilot/concepts/agents/copilot-cli/context-management
[about-cloud-agent]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-workshop]: ../../cloud/
