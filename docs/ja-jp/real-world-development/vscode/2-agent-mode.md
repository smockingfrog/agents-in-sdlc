---
title: "演習 2 - Copilot Agent Mode で機能を追加する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

アプリケーションに小さな変更を加える場合でも、通常は複数のファイルを更新し、テストを実行するなどの作業が必要です。開発者は、関連ファイルを探し、変更を加え、テストを実行し、問題をデバッグして、更新漏れのファイルを見つけてもう一度変更する、といった作業を行います。こうした手順は続いていきます。

Copilot Agent Mode を使うと、こうした作業を進めやすくなります。

Copilot Agent Mode は、IDE 内でより自律的に作業します。開発者と同様にプロジェクトの構造を調べ、必要なファイルを更新し、テストなどのタスクを実行して、見つかった問題を自動的に修正します。Agent Mode を使って、サイトに新しい機能を追加しましょう。

> [!NOTE]
> 名前は似ていますが、agent mode と cloud agent は異なる利用方法を想定しています。Agent mode は IDE 内で動作し、すばやいフィードバックと対話を可能にします。Cloud agent はチームの一員のように非同期で作業し、issue やプルリクエストを通じてやりとりします。

この演習では、次のことを学びます。

- Copilot Agent Mode がプロジェクトを調べ、関連ファイルを特定し、複数ファイルにわたる変更を行う方法。
- GitHub Copilot Agent Mode が UI とデータレイヤーにわたる新機能を実装する方法。
- Copilot Agent Mode が生成した変更とテストをレビューしてからコードベースにマージする方法。

## シナリオ

ゲームの一覧が増えてきたため、ユーザーがカテゴリーやパブリッシャーで絞り込めるようにします。前の演習でパブリッシャー ヘルパーを追加しました。次は Copilot Agent Mode を使って、残りのデータレイヤー、UI、テストを完成させます。

## Tailspin Toys の Web サイトを起動する

変更を加える前に、Tailspin Toys の Web サイトを確認し、現在の機能を把握しましょう。

Web サイトは、開発者向けのテーマでボードゲームのクラウドファンディングを提供します。ゲームの一覧を表示し、詳細を確認できます。単一の Astro アプリで、ビルド時に静的 HTML としてページを生成します。ページは frontmatter 内で Drizzle ORM を使ってローカルの SQLite データベースを直接参照します。別のバックエンド API やクライアント側 UI フレームワークはありません。再利用可能なデータアクセス ヘルパーは `src/lib/` に置き、インタラクティブな機能は標準 DOM API を使う小さなスコープ付き Astro `<script>` で追加します。

### Web サイトを起動する

Web サイトを簡単に起動できるように、単一の Astro 開発サーバーを起動する `npm run dev` タスクが用意されています。次の手順で GitHub Codespace から起動します。

1. Codespace に戻ります。前の演習の後なので、`main` にいるはずです。
2. Codespace 内で <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux) を押して、新しいターミナル ウィンドウを開きます。
3. フィルタリング作業用のブランチを作成して切り替えます。

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 次のコマンドを実行して Web サイトを起動します。

   ```bash
   npm run dev
   ```

   Astro 開発サーバーの準備ができると、次のような URL を示すメッセージが表示されます。

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> `http://localhost:4321` を開くよう促すダイアログが表示された場合は、**x** を選択して閉じます。

5. ターミナルで <kbd>Command</kbd> (Mac) または <kbd>Ctrl</kbd> (Windows/Linux) を押しながら Astro サーバーのアドレス `http://localhost:4321` を選択して、Web サイトを開きます。

> [!NOTE]
> Codespace のターミナルで localhost の URL を選択すると、`https://<your-codespace-name>-4321.app.github.dev/` に自動的にリダイレクトされます。これは Codespace の Web サーバーをホストするためのプライベート トンネルです。

### Web サイトを確認する

Web サイトを起動すると、現在の機能を確認できます。主な機能は次のとおりです。

- **ホーム ページ**: タイトル、画像、説明を含むボードゲームの一覧を表示します。
- **ゲーム詳細ページ**: ゲームを選択すると、タイトル、説明、パブリッシャー、カテゴリーなどの詳しい情報が表示されます。

## Copilot でバックログを調べる

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に Codespace に戻り、Copilot Chat パネルを開き、**New Chat** を選択して新しい会話を始めます。モードとモデルの選択は演習によって異なります。必要な場合は手順で説明します。

Web サイトの初期実装は動作していますが、新しい機能を追加して改善しましょう。まずバックログを確認します。テンプレートからプロジェクトを作成すると、issue のバックログも自動的に用意されます。GitHub Copilot に項目を表示してもらいましょう。

1. Chat ビューのエージェント ドロップダウンから **Agent** を選択します。**Agent** エージェントは自律的に計画を立て、複数ファイルにわたる変更を実装し、ターミナル コマンドを実行してツールを呼び出します。

   ![Chat ビューのエージェント ピッカーを示すスクリーンショット。](../../../_images/shared-chat-mode-selector.png)

2. 利用可能なモデル一覧から **Claude Sonnet 4.5** を選択します。

> [!CAUTION]
> このワークショップの作成者が、特定のモデルを推奨するものではありません。ワークショップの作成には Claude Sonnet 4.5 を使用したため、手順にも記載しています。提案されるコードに一貫性を持たせ、スムーズに進めることが目的です。大規模言語モデル (LLM) は確率的に動作するため、ワークショップの記載と異なる提案が表示されることがあります。これは通常の動作です。

> [!NOTE]
> LLM の確率的な性質により、Copilot が別の MCP コマンドを使う場合がありますが、タスクは同様に完了できるはずです。

3. 次のプロンプトを Copilot に送信して、バックログの issue を表示してもらいます。

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. **Continue** を選択して、すべての issue を一覧表示するコマンドを実行します。
5. 生成された issue の一覧を確認します。

Copilot がユーザーに最も役立つと考えた項目を優先順位付けしたことが分かります。

## 命令ファイルを確認する

エージェントにコードを生成させる前に、作業のコンテキストを Copilot に提供する命令ファイルを確認します。Web サイトへの機能追加の方針を説明する [user interface (UI) ファイル](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)を使います。

1. Codespace で `.github/instructions/ui.instructions.md` に移動します。
2. 機能追加に関する全般的なガイダンスを確認します。次の内容が含まれます。
   - アーキテクチャの概要。
   - コンポーネントの設計、テスト容易性、アクセシビリティに関する原則。
   - さまざまなファイル形式に対応する命令ファイルへのリンク:
     - Astro
     - Tailwind CSS
     - Drizzle

> [!TIP]
> 命令ファイルでは、ほかの命令ファイルやプロジェクト内のファイルを参照できます。パスは命令ファイルからの相対パスです。命令を再利用し、大きなファイルを扱いやすいサイズに分割し、コード生成の例やテンプレートを Copilot に示せます。

## フィルタリング機能を実装する

フィルタリングを完成させるには、少なくとも次の3種類の変更が必要です。

- データレイヤー (`src/lib/`) にフィルタリング ロジックを追加または調整する。
- フィルタリングの動作に関するテストを追加または更新する。
- ゲーム一覧ページにフィルタリング UI を追加する。

さらに、コードベースにマージする前にテストを実行し、すべて成功することを確認します。Copilot Agent Mode にこれらの作業を依頼しましょう。

1. 現在の Copilot 会話を続けるか、**New Chat** を選択して新しい会話を始めます。
2. **Add Context**、**Instructions**、**ui** の順に選択して命令ファイルを追加します。

   ![UI 命令ファイルを選択する例を示すスクリーンショット](../../../_images/ex3-select-instructions-file.png)

3. Chat ビューのエージェント ドロップダウンで、引き続き **Agent** が選択されていることを確認します。

   ![Chat ビューのエージェント ピッカーを示すスクリーンショット。](../../../_images/shared-chat-mode-selector.png)

4. モデルが **Claude Sonnet 4.5** のままであることを確認します。
5. バックログの関連 issue に基づいて機能を実装するよう、次のプロンプトを Copilot に送信します。

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Copilot がプロジェクトを調べ、必要なファイルを見つけて作業を始める様子を確認します。データレイヤーのヘルパー、UI、テストを探し、ファイルを変更してテストを実行します。

   ![Copilot がプロジェクト ファイルを調べる様子を示すスクリーンショット](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot はプロジェクトの調査、ファイルの編集、テストの実行などを行うため、複雑さやコードベースによって数分かかる場合があります。その間、エディターに **Keep** と **Undo** のボタンが表示されることがあります。作業中に選択する必要はなく、完了するとすべての変更に対して **Keep** または **Undo** が表示されます。

7. Copilot に求められたら、**Continue** を選択してテストを実行します。

   ![Copilot Chat パネルにテスト実行の確認ダイアログが表示されているスクリーンショット](../../../_images/ex3-agent-mode-run-tests.png)

8. 作業中に一時停止したり、テストが失敗したりすることがあります。問題ありません。Copilot はタスクが完了し、エラーを検出しなくなるまでコード生成とテストを繰り返します。

   ![Copilot Agent Mode による Chat セッションが完了した状態を示すスクリーンショット](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 生成されたコードに問題がないか確認します。

> [!CAUTION]
> Copilot などの AI ツールが生成したコードは、必ずレビューしてください。

10. Web サイトを起動したブラウザーに戻り、新しい機能を確認します。
11. 動作とコードのレビューが終わったら、Copilot Chat ウィンドウで **Keep** を選択します。

## まとめと次のステップ

おめでとうございます。この演習では、GitHub Copilot Agent Mode を使い、Tailspin Toys の Web サイトに機能を追加しました。次のことを学びました。

- GitHub Copilot Agent Mode が UI とデータレイヤーにわたる新機能を実装する方法。
- Copilot Agent Mode がプロジェクトを調べ、関連ファイルを特定し、複数ファイルにわたる変更を行う方法。
- Copilot Agent Mode が生成した変更とテストをレビューしてからコードベースにマージする方法。

次は[Playwright MCP サーバーで機能をテストし][next-lesson]、プルリクエストを作成します。

### ボーナスの演習 — ページネーションを実装する

ゲーム一覧が増えると、ページネーションが必要になります。この演習で学んだことを使い、Copilot にページネーションの実装を依頼しましょう。次の点を検討してください。

- 既存の命令ファイルを使うなど、現在のベスト プラクティスに従います。
- ユーザーがページ サイズを選べるようにするか、固定にするかなど、ページネーションの方式を決めます。
- 希望する実装に必要なガイダンスをプロンプトに含めます。
- GitHub Copilot にコンテキストや変更を依頼しながら、何度か改善する場合があります。これは Copilot を使う通常の作業です。

## リソース

- [Copilot の ask、edit、agent mode: 役割と使い分け][choose-mode]
- [VS Code の agent mode][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
