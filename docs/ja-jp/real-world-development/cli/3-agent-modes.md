---
title: "レッスン 3 - エージェント モード: Plan と Autopilot"
description: "Plan で方針を合意し、Autopilot で issue に基づくフィルタリングを実装し、Interactive モードで結果をレビューします。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

最初は小さな機能をプロジェクトに追加しました。しかし、大きな変更にはより堅実なプロセスが必要です。GitHub Copilot CLI は組織の既存の開発フローに沿って動作し、適切なものを適切な方法で構築できるよう支援します。このレッスンから、典型的なエージェント主導の開発プロセスをいくつかのレッスンにわたって実践します。issue から新機能を作り、コードの妥当性と機能の動作を確かめ、最終的にプロジェクトにマージします。

> [!NOTE]
> 機能開発を続ける間、同じ会話とブランチを使います。通常はファイルの種類ごとに異なるブランチや PR を使いますが、ここでは主要な概念に集中するため簡略化します。

このレッスンでは、次のことを行います。

- GitHub issue から新しい Copilot 会話を始めます。
- Plan モードで要件を定義します。
- Autopilot モードで新機能を実装します。
- コードをレビューします。
- 転送されたブラウザーで機能を手動で検証します。

機能開発を続ける中で、リポジトリの命令を更新し、既存の `quality-checks` スキルをカスタマイズし、MCP による検証を追加して QA エージェントを作成し、機能の PR を開きます。

## シナリオ

Tailspin Toys のカタログが増えてきたため、訪問者はカテゴリーやパブリッシャーでゲームを絞り込む必要があります。バックログの issue には機能が説明されていますが、複数カテゴリーの組み合わせなど、コーディング前に合意しておくべき詳細があります。Plan モードで決定事項を整理し、範囲を定めた実装を Autopilot に許可します。

## 背景

AI コーディング エージェントを開発フローに導入しても、基本は変わりません。むしろ、基本はこれまで以上に重要です。多くの開発者は、次のような流れで作業します。

1. 実装する内容を説明した登録済み issue から始めます。
2. 実装内容を計画します。
3. コードを作成してレビューします。
4. テストを実行して検証します。
5. 新機能を手動で検証します。
6. プルリクエスト (PR) を作成します。
7. コードレビューと継続的インテグレーションが成功したら、コードをマージします。

> [!NOTE]
> 具体的な手順はチームや組織によって異なりますが、ほとんどは上記の流れを基本にしています。

この標準的な手順に従うことで、AI が生成したコードが要件を満たし、手作業で書いたコードと同じレビュー プロセスを通るようにできます。

## 会話モード

**会話モード**は、エージェントの自律性を制御します。<kbd>Shift</kbd>+<kbd>Tab</kbd> を押すとモードを切り替えられます。

- **Interactive**: エージェントが変更を提案し、次に進む前にあなたの入力を待ちます。
- **Plan**: エージェントは計画を作成しますが、プロジェクト ファイルの編集はできません。
- **Autopilot**: エージェントはコードを書き、テストを実行し、タスクが完了するまで自律的に作業を繰り返します。

まず Plan モードで計画を確認し、その後 Autopilot で実装します。

## issue から始める

フィルタリングの作業を始める前に、Codespace に戻り、リポジトリとターミナルを準備します。

1. Codespace に戻ります。停止している場合は、再起動してから続けます。
2. 星評価の PR がマージ済みであることを確認します。
3. ターミナルが開いていない場合は、<kbd>Control</kbd>+<kbd>\`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux) を押します。
4. `main` を更新し、フィルタリング用のブランチを作成します。

   ```bash
   git checkout main
   git pull --ff-only
   git checkout -b game-filters-cli
   ```

5. Copilot CLI を起動します。

   ```bash
   copilot --yolo
   ```

6. <kbd>Tab</kbd> を2回押して **Issues** タブを開きます。
7. <kbd>A</kbd> を押してすべての issue を表示します。
8. 矢印キーで **Allow users to filter games by category and publisher** というタイトルの issue を選択します。
9. <kbd>C</kbd> を押して issue をプロンプトに追加し、**Session** タブに戻ります。

プロンプトの先頭に `#7` (または同様の番号) が表示されます。`#` を使うと、GitHub 上の issue やプルリクエスト (PR) をコンテキストに追加できます。

## フィルタリング機能を計画する

計画を立てることで、機能の実装やタスクの実行を Copilot に依頼する前に、アプローチを決められます。複雑な作業では、時間をかけて計画を立てることをお勧めします。Plan モードに切り替え、Copilot に計画を作成してもらいましょう。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> を押して Plan モードに切り替えます。プロンプトの下にあるモード表示が **Plan** になっていることを確認します。
2. 直前の手順で追加した issue の後に、次のプロンプトを入力します。

   ```plaintext
   Create a plan for implementing this feature.
   ```

Copilot はプロジェクトを調査し、最適な方法を検討して計画を作成します。

3. フィルターの動作について質問された場合は、希望に基づいて回答します。正解は一つではありません。
4. 計画ができたら、<kbd>Control</kbd>+<kbd>E</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>E</kbd> (Windows/Linux) を押して計画を展開します。
5. 計画を上下にスクロールしてレビューします。
6. 自分の決定と合わない部分があれば、Copilot に修正を依頼します。

## Autopilot を承認する

計画を作成してレビューしたら、実装に進みます。Autopilot を使って Copilot に作業を任せましょう。

Autopilot を使うと、Copilot はタスクが完了したと判断するまで問題に取り組みます。

1. **Accept plan and build on autopilot (recommended)**、またはインストール済みのバージョンに表示される同様のオプションを選択します。
2. プロンプトの下のモード表示が **Autopilot** になっていることを確認します。
3. Copilot が計画に沿って作業し、コードを生成してテストを実行する様子を確認します。

> [!NOTE]
> 承認すると、すぐに実装が始まる場合があります。先に計画をレビューしてください。Copilot が依存関係の不足やポートの競合を報告した場合は、チェック完了と見なす前にセットアップの問題を解決します。

## 実装をレビューして検証する

コードが生成されたら、ほかのコードと同様にマージ前にレビューします。コードを確認し、サイトを起動して問題がないか検証しましょう。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> を押して Interactive モードに切り替えます。モード表示に **Plan** や **Autopilot** が表示されていないことを確認します。
2. `/diff` を入力し、フィルタリングの実装とテストを確認します。
3. 結果を issue と計画時の決定事項と比較します。
4. コードをレビューしたら、<kbd>Esc</kbd> を押して差分表示を閉じます。
5. プロジェクトのチェック結果を確認し、失敗があれば Copilot に解決を依頼します。

## 新機能を試す

コードは問題なさそうですが、実際に動くでしょうか。前回と同じようにアプリを起動し、Codespaces の転送ポートからサイトを開きます。

1. Copilot にアプリの起動を依頼します。

   ```plaintext
   Start the app so I can try the filtering feature in my browser. Tell me the URL and leave the server running.
   ```

2. Codespaces でポート `4321` が利用可能と表示されたら、**Open in Browser** を選択します。
3. 計画で合意したカテゴリー、パブリッシャー、組み合わせでフィルターを試します。
4. リセット時と結果が空の場合の動作が、issue と合意事項に沿っていることを確認します。
5. Codespace に戻り、Copilot に起動した開発サーバーの停止を依頼します。

## まとめと次のステップ

さまざまな会話モードを使い、機能を作成してレビューしました。このレッスンでは、次のことを行いました。

- GitHub issue から新しい Copilot 会話を始めました。
- Plan モードで要件を定義しました。
- Autopilot モードで新機能を実装しました。
- コードをレビューしました。
- 転送されたブラウザーで機能を手動で検証しました。

次は[カスタム命令を使って][next-lesson]、コードが文書化された規約に従うようにする方法を確認しましょう。

## リソース

- [GitHub Copilot CLI の Autopilot][autopilot]
- [Copilot CLI コマンド リファレンス][cli-reference]

[autopilot]: https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[previous-lesson]: ../2-add-star-rating/
[next-lesson]: ../4-custom-instructions/
