---
title: "演習 4 - カスタム エージェント"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

## カスタム エージェントとは

GitHub Copilot の[カスタム エージェント][custom-agents-concept]を使うと、開発ワークフロー内の特定のタスクや分野に特化した AI アシスタントを作成できます。リポジトリの `.github/agents` フォルダーにある Markdown ファイルでエージェントを定義し、焦点を絞った命令、ベスト プラクティス、コーディング パターン、分野固有の知識を Copilot に与えて、特定の種類の作業をより効果的に実行できます。チームの専門知識を再利用可能なエージェントにできます。たとえば、[WCAG][wcag] 準拠をチェックするアクセシビリティ エージェント、安全なコーディング手法に従うセキュリティ エージェント、一貫したテスト パターンを適用するテスト エージェントがあります。

カスタム エージェントは、プロジェクト内の `.github/agents` フォルダー、またはユーザー共通の `~/.copilot/agents` にある Markdown ファイルで定義します。各ファイルには、少なくとも `name` と `description` を含む YAML frontmatter と、その後にエージェントの動作、専門知識、命令を定義する Markdown プロンプトを記述します。

### カスタム エージェントと agent skill の比較

カスタム エージェントと [agent skill][agent-skills-concept]には、役割の重なる部分があります。どちらも主に Markdown ファイルで定義され、AI に作業の方法を指示します。違いを簡潔に表すと、**カスタム エージェントは作業者、スキルはツール**です。

カスタム エージェントは独自のコンテキスト ウィンドウを持ち、スキルやほかのエージェントを調整しながら作業できます。このラボでは、アクセシビリティ カスタム エージェントがアクセシビリティ ガイドラインに沿ってサイトをレビューし、更新します。その作業の一部として、プルリクエスト ワークフローのスキルやテスト実行・管理のスキルなどを呼び出すこともできます。

> [!NOTE]
> カスタム エージェントの作成に唯一の正解はありません。AI に関するほかの作業と同じように、環境やシナリオに合わせて試し、改善してください。

[custom-agents-concept]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[agent-skills-concept]: https://docs.github.com/copilot/concepts/agents/about-agent-skills
[wcag]: https://www.w3.org/WAI/standards-guidelines/wcag/

## シナリオ

Tailspin Toys は、視覚的な能力や好みにかかわらず、すべてのユーザーがクラウドファンディング プラットフォームを利用できるようにしたいと考えています。最近、現在のダーク テーマでは背景色と文字色のコントラストが十分でなく、読みにくいと感じるユーザーがいることが分かりました。このアクセシビリティの問題に対処するため、デザイン チームはユーザーが切り替えられるハイ コントラスト モードを求めています。

アクセシビリティは重要なので、この機能をできるだけ早く実装します。カスタム エージェントに機能を生成させましょう。

この演習では、次のことを行います。

- 既存のアクセシビリティ カスタム エージェントを確認します。
- Copilot Chat のアクセシビリティ エージェントを使ってハイ コントラスト モードを実装します。

## アクセシビリティ カスタム エージェントを確認する

アクセシビリティ用のカスタム エージェントはすでに作成されています。Copilot を導く方法を理解するため、内容を確認しましょう。

Codespace に戻り、ターミナルを開いて、アクセシビリティ作業用に `main` から新しいブランチを作成して切り替えます (演習 3 のフィルタリング PR とは分けて作業します)。

```bash
git checkout main
git pull
git checkout -b accessibility-vscode
```

1. `.github/agents/accessibility.md` を開きます。
2. `name` と `description` フィールドがある YAML frontmatter を確認します。

> [!CAUTION]
> カスタム エージェントには、`name` と `description` を含む frontmatter が必要です。

3. 続くセクションを読み、次の内容を確認します。
   - アクセシブルな Web サイトのコードを生成する際の主な責任。
   - アクセシビリティのベスト プラクティス。
   - HTML、CSS、JavaScript のコード例。
   - よくある問題と間違い。

## Copilot Chat でカスタム エージェントを使う

VS Code の Copilot Chat ビュー下部にあるエージェント ドロップダウンには、`.github/agents` に定義されたすべてのカスタム エージェントが表示されます。カスタム エージェントを選択すると、そのエージェントの命令とツールに合わせてチャット セッションが設定されます。

> [!TIP]
> **Copilot Chat を開く**
>
> 次の演習を始める前に Codespace に戻り、Copilot Chat パネルを開き、**New Chat** を選択して新しい会話を始めます。モードとモデルの選択は演習によって異なります。必要な場合は手順で説明します。

1. Copilot Chat ビューのエージェント ドロップダウンで、まだ選択されていない場合は **Agent** を選びます。

   ![Chat ビューのエージェント ピッカーを示すスクリーンショット。](../../../_images/shared-chat-mode-selector.png)

2. チャット ビューの下部にあるエージェント ドロップダウン (現在のエージェントを表示する欄。既定では **default**) を選択します。
3. 利用可能なエージェント一覧から **Accessibility agent** を選択します。
4. アクセシビリティ エージェントに次のプロンプトを送信します。

    ```plaintext
    Add a high-contrast mode to the site. There should be a toggle for high contrast which the user can set, and the setting should persist across page reloads using local storage on the browser.
    ```

5. Copilot Chat がコードベースを調べ、変更を提案してプロジェクト ファイルを編集します。編集ごとに、ファイル パスと差分がチャットに表示されます。
6. エージェントが変更した作業ファイルの一覧は、**Files changed** の表示で更新されます。

> [!NOTE]
> この処理には数分かかることがあります。Copilot は Astro コンポーネント、CSS、関連テストなどの既存ファイルを実際に変更します。

次の演習では、進行中の作業を確認し、指示を追加します。

## まとめと次のステップ

このレッスンでは、特定のタスクや分野に特化した AI アシスタントである、GitHub Copilot の[カスタム エージェント][custom-agents]を確認しました。カスタム エージェントを使うと、チームの専門知識や標準を再利用可能なエージェントに組み込めます。

このレッスンでは、次のことを行いました。

- カスタム エージェントの定義方法を確認しました。
- Copilot Chat の agent mode でカスタム エージェントを使いました。

次は[エージェントの作業を監視して指示を追加し][next-lesson]、変更を確認しながら、同じセッションにライト モードの切り替えを追加します。

## リソース

- [カスタム エージェントについて][custom-agents]
- [IDE でカスタム エージェントを作成する][creating-custom-agents-ide]
- [VS Code のカスタム エージェント][custom-agents-vscode]
- [カスタム エージェントの構成][custom-agents-config]
- [awesome-copilot のカスタム エージェント][awesome-copilot-agents]

---

| [← 前のレッスン: Playwright MCP サーバーで機能をテストする][previous-lesson] | [次のレッスン: エージェントの監視と管理 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-mcp/
[next-lesson]: ../5-managing-agents/
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[creating-custom-agents-ide]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/create-custom-agents-in-your-ide
[custom-agents-vscode]: https://code.visualstudio.com/docs/copilot/customization/custom-agents
[custom-agents-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
[awesome-copilot-agents]: https://github.com/github/awesome-copilot/tree/main/agents
