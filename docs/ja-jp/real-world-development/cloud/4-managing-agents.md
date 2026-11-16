---
title: "演習 4 - エージェントの監視と管理"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

直前の演習では、Copilot cloud agent にユーザー エクスペリエンスの改善や機能追加を目的とする3つのタスクを依頼しました。cloud agent は非同期かつ自律的に作業しますが、タスクの進行状況を監視することも重要です。

cloud agent に割り当てたタスクを管理する方法はいくつかあります。[GitHub.com の agents ページ][agents-page]もその一つです。この Mission control では、オープンなプルリクエスト (PR) があるすべてのエージェント タスクを確認できます。実行内容を調べたり、進行中のセッションに指示を追加したりできます。

このレッスンでは、次のことを行います。

- agents ページで cloud agent のタスクを監視します。
- 進行中のセッションに指示を追加し、機能を追加で依頼します。

## シナリオ

ハイ コントラスト モードの作成をエージェントに依頼した後、チームはライト モードも追加したいと考えました。サイトのスタイルを更新し、切り替え機能を追加しているため、ライト モードも含めるのが自然です。エージェントに指示を追加し、ハイ コントラストに加えてライト モードも実装してもらいましょう。

## Copilot cloud agent のタスクを確認する

Copilot cloud agent に割り当てたすべてのタスクの状態を確認しましょう。

1. [Copilot agents ページ](https://github.com/copilot/agents)に移動します。
2. メイン ペインと左ペインの両方に表示されるタスク一覧を確認します。次のような、Copilot に割り当てたタスクが表示されます。
   - コードベースのドキュメントを更新する。
   - ゲーム変更用の API を生成する。
   - Web サイトにハイ コントラスト モードを追加する。
3. 実行中のタスクを選択し、Copilot が行った作業を確認します。次のような内容があります。
   - リポジトリからコードをチェックアウトする。
   - Copilot の作業環境を作成する。
   - MCP サーバーをセットアップする。
   - 割り当てられたタスクを完了するためのさまざまな作業を実行する。

> [!NOTE]
> 表示される手順は、Copilot の作業状況や採用した方法によって異なります。

4. 右側に表示されるプルリクエスト (PR) ペインも確認します。PR と変更されたファイルを確認し、さらに詳しく監視できます。

## Cloud agent に指示を追加する

現在実行中のタスクを確認したので、ハイ コントラスト モードの作業中にライト モードも追加するよう Copilot に依頼します。

1. ハイ コントラスト モードを追加するセッションを選択します。エージェントが付けた名前や作業状態によって、タイトルは異なります。

    ![Mission control に表示されたアクセシビリティ セッション](../../../_images/ex6-accessibility-session.png)

2. セットアップが完了して作業が始まったことが分かるメッセージが表示されるまで、数分間セッションを確認します。
3. **Steer active session while Copilot is working** ダイアログに次のプロンプトを入力します。

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![agents ページの cloud agent タスク画面で、作業中のセッションに指示を追加するダイアログが強調表示されている](../../../_images/ex6-steer-cloud-agent-task.png)

4. <kbd>Enter</kbd> を押してプロンプトを送信します。
5. Copilot がプロンプトを受け取り、作業に取り込むことを確認します。

## Copilot の作業を待つ

前回と同じように、Copilot は更新されたタスクに取り組みます。メッセージを送信した時点で作業中の手順を終えると、新しい依頼が作業に反映されます。

作業には数分かかります。その間に、ここまでに学んだことを振り返りましょう。

## まとめと次のステップ

このレッスンでは、GitHub Copilot cloud agent のタスクを監視し、指示を追加する中心的な場所である Copilot agents ページを確認しました。Mission control では、実行中および完了したタスクを追跡し、進行中の作業を確認して、作業の範囲を変更したり追加のガイダンスを提供したりできます。

このレッスンでは、次のことを行いました。

- Mission control と agents ページで cloud agent のタスクを監視しました。
- 進行中のセッションに指示を追加して、機能を追加で依頼しました。

Copilot がアクセシビリティ機能を実装している間に、次は[Copilot が作成したプルリクエストを改善します][next-lesson]。

## リソース

- [Copilot agents ページ][agents-page]
- [カスタム エージェントについて][custom-agents]

---

| [← 前のレッスン: カスタム エージェント][previous-lesson] | [次のレッスン: Copilot の作業を改善する →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
