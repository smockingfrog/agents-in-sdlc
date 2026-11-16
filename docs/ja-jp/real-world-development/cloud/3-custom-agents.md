---
title: "演習 3 - カスタム エージェント"
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

このレッスンでは、カスタム エージェントを使って次のことを確認します。

- カスタム エージェントの定義方法。
- カスタム エージェントへのタスク割り当て。

## シナリオ

Tailspin Toys は、視覚的な能力や好みにかかわらず、すべてのユーザーがクラウドファンディング プラットフォームを利用できるようにしたいと考えています。最近、現在のダーク テーマでは背景色と文字色のコントラストが十分でなく、読みにくいと感じるユーザーがいることが分かりました。このアクセシビリティの問題に対処するため、デザイン チームはユーザーが切り替えられるハイ コントラスト モードを求めています。

アクセシビリティは重要なので、この機能をできるだけ早く実装します。カスタム エージェントに機能を生成させましょう。

## アクセシビリティ カスタム エージェントを確認する

アクセシビリティ用のカスタム エージェントはすでに作成されています。Copilot を導く方法を理解するため、内容を確認しましょう。

Codespace に戻り、アクセシビリティ カスタム エージェントのファイルを確認します。

1. `.github/agents/accessibility.md` を開きます。
2. `name` と `description` フィールドがある YAML frontmatter を確認します。

> [!CAUTION]
> カスタム エージェントには、`name` と `description` を含む frontmatter が必要です。

3. 続くセクションを読み、次の内容を確認します。
   - アクセシブルな Web サイトのコードを生成する際の主な責任。
   - アクセシビリティのベスト プラクティス。
   - HTML、CSS、JavaScript のコード例。
   - よくある問題と間違い。

## issue を作成して割り当てる

Mission control は、環境内のすべてのエージェントを操作する中心的な場所です。Copilot cloud agent にタスクを割り当て、進行状況を監視し、作業中のセッションに指示を追加できます。まず、ハイ コントラスト モードの作成を Copilot に依頼しましょう。

1. リポジトリに移動します。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい issue ダイアログを開きます。
4. **Blank issue** を選択して、新しい issue を作成します。
5. **Title** に `Add high contrast mode to website` と入力します。
6. **Description** に次の内容を入力します。

    ```plaintext
    We need a high contrast mode for the site. There should be a toggle for high contrast which the user can set. It should store the setting in local storage on the browser.
    ```

7. **Create** を選択して issue を作成します。
8. 右側にある **Assign to Copilot** を選択して、割り当てダイアログを開きます。
9. カスタム エージェント一覧から **Accessibility agent** を選択します。

    ![カスタム エージェントとアクセシビリティ エージェントが強調表示された cloud agent の割り当て画面](../../../_images/ex5-select-custom-agent.png)

10. **Assign** を選択します。
11. Copilot がバックグラウンドで作業を始めます。

## まとめと次のステップ

このレッスンでは、特定のタスクや分野に特化した AI アシスタントである、GitHub Copilot の[カスタム エージェント][custom-agents]を確認しました。カスタム エージェントを使うと、チームの専門知識や標準を再利用可能なエージェントに組み込めます。

このレッスンでは、次のことを行いました。

- カスタム エージェントの定義方法を確認しました。
- エージェントにタスクを割り当てました。

Copilot がハイ コントラスト モードの実装に取り組んでいる間に、次は [Mission control からエージェント セッションを監視し、指示を追加します][next-lesson]。

## リソース

- [カスタム エージェントについて][custom-agents]
- [組織でカスタム エージェントを使うための準備][org-custom-agents]
- [エンタープライズでカスタム エージェントを使うための準備][enterprise-custom-agents]

---

| [← 前のレッスン: GitHub Copilot cloud agent][previous-lesson] | [次のレッスン: エージェントの監視と管理 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../2-cloud-agent/
[next-lesson]: ../4-managing-agents/
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[org-custom-agents]: https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents
[enterprise-custom-agents]: https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents
