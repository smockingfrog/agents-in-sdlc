---
title: "演習 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: カスタム命令][previous-lesson] |
|:--|

技術的負債に悩んでいない組織は、ほとんどありません。未解決のセキュリティ問題、更新が必要なレガシー コード、実装する時間がなくバックログに残った機能要求などがあります。GitHub Copilot の cloud agent は、コードの更新や機能の追加といったタスクを自律的に実行できます。作業が終わると、進捗を追跡できるドラフト PR を作成し、環境を準備してタスクに取り組み始めます。作業中または完了後にセッションを詳しく確認できます。提案された解決策のレビュー準備ができると、プルリクエストで通知されます。

このレッスンでは、Copilot cloud agent を使って次のことを行います。

- コードを生成する環境をカスタマイズする。
- 操作を安全に行う。
- 範囲を明確に定めた issue の重要性を理解する。
- issue を Copilot に割り当てる。

## シナリオ

Tailspin Toys には対処したい技術的負債があります。最初の Web サイトを作成した委託先は、ドキュメントを十分に作成していませんでした。まず、アプリケーションのエクスポートされたすべての関数に TSDoc のドキュメント コメントを追加します。

さらに、デザイン チームはゲームを見つけやすくしたいと考えています。各ゲームの詳細ページに、同じカテゴリーのほかのゲームを表示し、閲覧を続けられるようにします。まだ仕上げに時間をかける必要はありませんが、UX の受け入れテストに使える状態にしたいと考えています。この作業は現在のブロッカーですが、優先度がより高い issue もあります。

どちらも、優先度が下がりやすいタスクです。Copilot cloud agent に割り当てると、非同期に作業を進める間、開発者は別のタスクに集中できます。その後で作業内容をレビューし、期待どおりであることを確認できます。

## GitHub Copilot cloud agent について

[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) は、人間の開発者と同様にバックグラウンドでタスクを実行できます。人間の開発者に仕事を依頼するときと同じように、[GitHub issue を Copilot に割り当てる](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)方法などがあります。割り当てると、Copilot は進行状況を追跡するドラフト プルリクエストを作成し、環境をセットアップしてタスクを始めます。処理中でも完了後でも、Copilot のセッションを確認できます。解決策のレビュー準備ができると、プルリクエストであなたに通知します。

## 範囲を明確にした指示の重要性

GitHub Copilot に魔法はありません。考える作業を省略できるわけではありません。単純に見える操作でも、詳しく調べると複雑さが見えてきます。[Copilot cloud agent に依頼するタスクの範囲を慎重に決めてください][cloud-agent-best-practices]。AI ペア プログラマーとして、段階を踏み、学び、試し、必要に応じて調整しましょう。生成 AI を加えても、ソフトウェア開発の基本は変わりません。

## このリポジトリのカスタム命令

前の演習では、カスタム命令と、Copilot の作業を導く方法を確認しました。カスタム命令の演習をすでに完了していれば `.github/copilot-instructions.md` を実際に確認しています。まだの場合は、この機会に見てみましょう。Copilot cloud agent に作業を割り当てる前に、リポジトリに含まれる命令ファイルを読むだけで確認し、後からその影響を探せるようにします。

リポジトリの GitHub Web UI、または起動済みの Codespace で次のファイルを開きます。

- `.github/copilot-instructions.md`: **Code standards** セクションを確認します。特に、TypeScript の規約と TSDoc のドキュメント コメントに関する要件を見ます。
- `.github/instructions/unit-tests.instructions.md`: `applyTo` frontmatter を確認します。この命令は `**/*.test.ts` に一致するファイルに適用されます。

次のセクションで `Code lacks documentation` issue を cloud agent に割り当てると、プルリクエストに TSDoc コメント、TypeScript の規約、コメント ヘッダーが反映されます。これらの命令ファイルが影響した結果を、後の演習で PR をレビューするときに確認します。

## Copilot cloud agent の開発環境を準備する

誰がコードを書く場合でも、通常は特定の環境と、状態を整えるためのセットアップ スクリプトが必要です。ソフトウェア エンジニアのように作業する Copilot にタスクを割り当てる場合も同じです。

Cloud agent は GitHub Actions を使って作業環境を構築します。`.github/workflows/copilot-setup-steps.yml` に設定した[専用のセットアップ ワークフロー][setup-workflow]を作成すると、作業を始める前に必要な開発ツールと依存関係を利用できるようになります。学習に集中できるよう、このラボでは事前に設定されています。単一の Astro アプリ用に Node.js、プロジェクトの依存関係、エンドツーエンド テスト用のブラウザー バイナリ、マイグレーションとシードが済んだ SQLite データベースを Copilot が利用できるようにします。

```yaml
name: "Copilot Setup Steps"

# リポジトリの "Actions" タブからセットアップ手順をテストできます
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # 手順に必要な最小限の権限を設定します。Copilot は操作用の独自トークンを受け取ります
    permissions:
      # セットアップでリポジトリをクローンする場合 (依存関係のインストールなど) は `contents: read` が必要です。クローンしない場合は、手順の完了後に Copilot が自動的にクローンします
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # フロントエンド / アプリのセットアップ - Node.js (アプリ全体は Astro + Drizzle/libSQL)
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # ローカルの SQLite データベースをマイグレーションし、シードしてビルドとテスト用のデータを準備します
      - name: Set up the database
        run: npm run db:setup
```

一般的な GitHub ワークフローと同じ形式ですが、いくつか重要な点があります。

- `copilot-setup-steps` という単一のジョブが含まれています。このジョブは Copilot がプルリクエストの作業を始める前に GitHub Actions で実行されます。
- `workflow_dispatch` トリガーが設定されているため、リポジトリの **Actions** タブから手動でワークフローを実行できます。Copilot が実行するまで待たずに、正常に動くかテストできます。

## ドキュメントを追加する

ドキュメントの重要性は広く理解されていますが、情報が古かったり不足していたりするプロジェクトは少なくありません。こうした技術的負債は放置されやすく、生産性を下げ、コードベースの保守や新しい開発者の参加を難しくします。Copilot はドキュメントの作成に役立つため、Copilot cloud agent に依頼するのに適したタスクです。バックグラウンドで必要なドキュメントを作成します。後の演習で結果を確認しましょう。

1. 新しいブラウザー タブで github.com のリポジトリを開きます。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい issue ダイアログを開きます。
4. **Blank issue** を選択して、新しい issue を作成します。
5. **Title** に `Code lacks documentation` と入力します。
6. **Description** に次の内容を入力します。

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. **Create** を選択して issue を作成します。
8. 右側にある **Assign to Copilot** を選択して、割り当てダイアログを開きます。

  ![issue に Copilot を割り当てる](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。
10. **Pull Requests** タブを選択します。
11. 新しく作成されたプルリクエスト (PR) を開きます。タイトルは `[WIP]: Code lacks documentation` のようなものになります。一覧に PR が表示されない場合は、少し待ってからブラウザーを更新してください。
12. 数分後、Copilot が todo リストを作成していることを確認します。

> [!NOTE]
> Copilot が PR の todo リストを作成するまで数分かかることがあります。Copilot は、先ほど説明したワークフローを実行して環境を準備し、プロジェクトを分析して、問題に取り組む方法を検討しています。

13. todo リストと、実行予定のタスクを確認します。
14. プルリクエストのタイムラインを下にスクロールし、Copilot が issue の作業を開始したことを示す更新を確認します。
15. **View session** ボタンを選択します。

  ![Copilot のセッションを表示する](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 最新の状態を示す表示を確認するには、ウィンドウの更新が必要な場合があります。

16. セッションをリアルタイムでスクロールし、Copilot が問題を解決する様子を確認します。コードを調べて状態を理解し、方針を考えて、コードを作成する様子を見ることができます。

処理には数分かかることがあります。Copilot cloud agent の主な目的は、作業を非同期で進めて開発者が別のタスクに集中できるようにすることです。別のタスクを Copilot cloud agent に割り当て、その間にアプリの機能を実装する作業を進めましょう。

## ゲーム詳細ページに関連ゲームのセクションを追加する

GitHub Copilot cloud agent の大きな利点は、作業を分けられることです。デザイン チーム向けに関連ゲームのセクションを追加する作業は長くかからないかもしれませんが、その時間を別の作業に使えます。Copilot cloud agent に割り当ててみましょう。

1. github.com のリポジトリに戻ります。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい issue ダイアログを開きます。
4. **Blank issue** を選択します。
5. **Title** に `Show related games on the game details page` と入力します。
6. **Description** に次の内容を入力します。

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. **Create** を選択して issue を作成します。
8. 右側にある **Assign to Copilot** を選択して、割り当てダイアログを開きます。

  ![issue に Copilot を割り当てる](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

ほどなく issue の最初のコメントに 👀 が表示され、Copilot が作業を始めたことがわかります。

![Copilot が目の絵文字で issue の作業中であることを示す](../../../_images/ex4-issue-eyes-emoji.png)

Copilot は2つ目の依頼にも取り組んでいます。Copilot cloud agent はソフトウェア エンジニアのように作業するため、常時監視する必要はありません。完了後にレビューできます。次はカスタム エージェントの作成と使用を確認しましょう。

## まとめと次のステップ

このレッスンでは、[GitHub Copilot cloud agent][copilot-agents]を確認しました。Cloud agent に issue を割り当てると、非同期で作業を実行できます。技術的負債への対処、新機能の作成、別のフレームワークへのコード移行などを依頼できます。

このレッスンでは、次のことを行いました。

- コードを生成する環境をカスタマイズしました。
- 操作を安全に行うことを確認しました。
- 範囲を明確に定めた issue の重要性を確認しました。
- issue を Copilot に割り当てました。

Copilot cloud agent がバックグラウンドで作業している間に、カスタム エージェントの作成と使用に進みます。[Copilot cloud agent は MCP サーバーも利用でき][cloud-agent-mcp]、前のモジュールで扱ったカスタム命令も利用できます。

## リソース

- [Copilot cloud agent について][copilot-agents]
- [GitHub issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent のセットアップ ワークフローのベスト プラクティス][cloud-agent-best-practices]

| [次のレッスン: カスタム エージェント →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
