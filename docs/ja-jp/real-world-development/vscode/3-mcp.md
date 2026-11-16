---
title: "演習 3 - Playwright MCP サーバーで機能をテストする"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: Agent mode][previous-lesson] · [次のレッスン: カスタム エージェント →][next-lesson]

agent mode でフィルタリング機能を作成しました。プルリクエストを開く前に、ブラウザーで実際に動くことを確認しましょう。自分でアプリを操作する代わりに、**Playwright MCP サーバー**を接続し、Copilot に実際のブラウザーを操作させて機能をテストします。その後、ブランチを公開して PR を開きます。

この演習では、次のことを行います。

- Model Context Protocol (MCP) と MCP サーバーが Copilot にツールを追加する仕組みを学びます。
- Playwright MCP サーバーをワークスペースに追加します。
- Copilot に Playwright MCP を使わせて、ブラウザーでフィルタリング機能を手動テストします。
- ブランチを公開して、フィルタリング作業のプルリクエストを開きます。

## Model Context Protocol (MCP) とは

agent mode は、エディターの外部に接続できるとさらに便利になります。Copilot がそのために使うのが Model Context Protocol (MCP) です。エージェントが外部のツールやサービスと通信するための標準的な方法です。

![agent mode の内部と、コンテキスト、LLM、MCP サーバーや VS Code 拡張機能が提供するツールとのやりとりを示す図](../../../_images/ex1-mcp-diagram.png)

[Model Context Protocol (MCP)](https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/) を使うと、AI エージェントは外部のツールやサービスと通信できます。MCP を使うことで、リソースを使って最新情報にアクセスしたり、ツールを使って依頼に応じた操作を実行したりできます。

こうしたツールやリソースには、AI エージェントと外部サービスの橋渡しをする MCP サーバーを介してアクセスします。MCP サーバーは、AI エージェントと外部ツール (既存の API や npm パッケージなどのローカル ツール) の通信を管理します。各 MCP サーバーは、AI エージェントが利用できる一連のツールやリソースを提供します。

よく使われる MCP サーバーには、次のものがあります。

- **[GitHub MCP Server](https://github.com/github/github-mcp-server)**: GitHub リポジトリを管理する API にアクセスできます。AI エージェントは、新しいリポジトリの作成、既存リポジトリの更新、issue やプルリクエストの管理などを実行できます。
- **[Playwright MCP Server](https://github.com/microsoft/playwright-mcp)**: Playwright を使ったブラウザー自動化機能を提供します。AI エージェントは Web ページの移動、フォームの入力、ボタンの選択などを実行できます。

ほかにも多くの MCP サーバーがさまざまなツールやリソースを提供しています。GitHub は、エコシステム内の発見や貢献を促進するために[MCP レジストリ](https://github.com/mcp)をホストしています。

> [!CAUTION]
> セキュリティの観点から、MCP サーバーはプロジェクトのほかの依存関係と同様に扱ってください。使用前にソース コードをよく確認し、発行元を検証したうえで、セキュリティへの影響を検討してください。信頼できる MCP サーバーだけを使い、機密性の高いリソースや操作へのアクセスを許可する際は注意してください。

## MCP の構成を確認する

`.vscode/mcp.json` ファイルで、この VS Code ワークスペースから利用できる MCP サーバーを設定します。

1. Codespace で `.vscode/mcp.json` を開きます。
2. 次のように `github` サーバーが設定されていることを確認します。

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        }
      }
    }
    ```

`github` の設定はプロジェクト テンプレートに含まれているため、前の演習で Copilot がバックログの issue を読み取れました。これは[リモート GitHub MCP サーバー][remote-github-mcp-server]を使うため、ローカルでインストールする必要はありません。VS Code は HTTP 経由で接続し、Copilot が最初にツールを使うときに GitHub で認証します。

## Playwright MCP サーバーを追加する

次は2つ目のサーバーを追加します。[Playwright MCP サーバー][playwright-mcp-server]は Copilot に操作可能なブラウザーを提供します。機能をテストするために使います。

1. `.vscode/mcp.json` で `github` の隣に `playwright` の設定を追加し、ファイルを次のようにします。

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        },
        "playwright": {
          "command": "npx",
          "args": ["@playwright/mcp@latest", "--headless"]
        }
      }
    }
    ```

2. ファイルを保存します。

リモートの `github` サーバーとは異なり、`playwright` はローカル サーバーです。VS Code は `npx @playwright/mcp@latest` を実行して起動します。`--headless` フラグを指定すると、ブラウザー ウィンドウを表示せずに実行できます。Codespace には表示用のデスクトップがないため、この設定が必要です。

> [!NOTE]
> Tailspin Toys プロジェクトはエンドツーエンド テストで Playwright をすでに使っているため、通常は Playwright 用ブラウザーがインストール済みです。後で Copilot がブラウザーがないと報告した場合は、`npx playwright install chromium` を実行するよう依頼して、もう一度試してください。

## サーバーを起動して信頼する

VS Code は MCP サーバーを**必要に応じて起動**します。Copilot がサーバーのツールを初めて使うときに VS Code が起動し、サーバーを信頼するか確認します。

1. `.vscode/mcp.json` で `playwright` の設定の上に **Start** が表示されます。選択してサーバーを起動し、構成が正しいことを確認します。
2. サーバーを信頼するか確認されたら、構成をレビューして信頼することを選択し、起動できるようにします。

手動で起動する必要はありません。省略した場合は、次の手順で Copilot が Playwright のツールを初めて使うときに VS Code がサーバーを起動し、信頼するか確認します。

## フィルタリング機能をテストする

[Playwright MCP サーバー][playwright-mcp-server]は、Copilot に実際のブラウザーを提供します。自分でアプリを操作して確認する代わりに、エージェントにページを開かせ、移動し、フィルターを適用させて、結果を読み上げてもらえます。会話を離れずに機能の動作を確かめる簡単な方法です。

Playwright MCP サーバーは、スクリーンショットではなくページの[アクセシビリティ ツリー][playwright-mcp-server]を使って操作します。つまり、エージェントは構造化されたラベル付きの要素 (ボタン、リンク、リスト項目など) を基に判断します。支援技術と同じように操作するため、簡単な機能テストをアクセシビリティの確認にも活用できます。

サーバーを接続し、アプリを起動したら、次のプロンプトを Copilot に送信してフィルタリング機能を操作してもらいます。

```text
Using the Playwright MCP server, open a browser to the running app at http://localhost:4321 and verify the new game filtering feature:

1. Go to the games page and note how many games are listed.
2. Apply a category filter and confirm the list updates to only show games in that category.
3. Clear it, then apply a publisher filter and confirm the list updates to that publisher.
4. Combine a category and a publisher filter and confirm the results respect both.

Report what you observe at each step, and call out anything that does not behave as expected.
```

Copilot は Playwright MCP サーバーを通じてブラウザーを起動し、各手順を実行して、結果を報告します。要約を issue の受け入れ条件と照らし合わせます。問題があれば、追加の質問をするか、コードを修正するよう依頼してからプルリクエストを開きます。

> [!NOTE]
> テストするには、アプリを `http://localhost:4321` で起動しておく必要があります。開発サーバーを停止した場合は、プロンプトを送る前にもう一度起動してください。Copilot が Playwright MCP サーバーを初めて使うときに、ブラウザーをダウンロードする必要がある場合があります。ブラウザーがないと報告されたら、`npx playwright install chromium` を実行するよう依頼して、もう一度試してください。

[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp

## ブランチを公開してプルリクエストを作成する

機能が動作することを確認したので、チームのレビュー用にプルリクエスト (PR) を作成します。まず `filtering-vscode` ブランチを公開します。

1. Codespace の **Source Control** パネルに移動し、Copilot が行った変更を確認します。
2. **+** アイコンを選択して変更をステージします。
3. **Sparkle** ボタンを選択してコミット メッセージを生成します。

   ![Copilot が行った変更を示す Source Control パネルのスクリーンショット](../../../_images/ex3-source-control-changes.png)

4. **Publish** を選択して、ブランチをリポジトリにプッシュします。

## プルリクエストを作成する

github.com や GitHub コマンド ライン インターフェイス (CLI) など、プルリクエストを作成する方法はいくつかあります。GitHub Copilot を使っているので、PR も作成してもらいましょう。関連する issue を見つけ、PR と関連付けられます。

1. Copilot Chat パネルに移動し、**New Chat** を選択して新しいセッションを開始します。
2. エージェント ドロップダウンで **Agent** が選択されていることを確認します。これで Copilot が GitHub ツールを使えます。
3. 次のプロンプトで PR の作成を依頼します。

   ```plaintext
   Find the issue in the repo related to filtering by category and publisher. Create a new pull request for the current branch, and associate it with the correct issue.
   ```

4. 必要に応じて **Continue** を選択し、Copilot が情報を集めて操作を実行できるようにします。GitHub ツールを初めて使うときは、GitHub での認証を求められることがあります。画面の指示に従って認証します。
5. Copilot が issue を検索して該当の issue を見つけ、PR を作成する様子を確認します。
6. Copilot が表示したリンクを選択してプルリクエストを確認します。ただし、**まだマージしないでください**。

## まとめと次のステップ

おめでとうございます。この演習では、次のことを行いました。

- Model Context Protocol (MCP) と MCP サーバーが Copilot にツールを追加する仕組みを学びました。
- Playwright MCP サーバーをワークスペースに追加しました。
- ブラウザーでフィルタリング機能を手動テストするために Playwright MCP を使いました。
- ブランチを公開し、フィルタリング作業のプルリクエストを開きました。

MCP サーバーを使って機能を**テスト**しましたが、Copilot を拡張する方法は MCP だけではありません。次は[カスタム エージェントを作成し][next-lesson]、アクセシビリティのレビューなど、目的を絞ったタスクを効率化します。

## リソース

- [MCP とは何か、なぜ話題になっているのか][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [GitHub MCP Server][github-mcp-server]
- [GitHub MCP Registry][mcp-registry]
- [VS Code の MCP サーバー][vscode-mcp-config]

[previous-lesson]: ../2-agent-mode/
[next-lesson]: ../4-custom-agents/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[github-mcp-server]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[remote-github-mcp-server]: https://github.blog/changelog/2025-06-12-remote-github-mcp-server-is-now-available-in-public-preview/
[vscode-mcp-config]: https://code.visualstudio.com/docs/agents/reference/mcp-configuration
