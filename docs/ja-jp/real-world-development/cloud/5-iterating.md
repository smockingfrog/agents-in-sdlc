---
title: "演習 5: GitHub Copilot の作業を改善する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

## 作業をレビューする

このラボでは、GitHub Copilot を使い、ユーザー エクスペリエンスの改善と機能追加に取り組みました。コードにドキュメントを追加し、デザイン チームが改善を検討する関連ゲーム機能を作り、ハイ コントラストとライト モードの切り替えを含むアクセシビリティ機能を実装するよう依頼しました。コードの変更を確認し、必要に応じて Copilot にフィードバックを送って改善しましょう。

### シナリオ

生成 AI を加えても、ソフトウェア設計と DevOps の基本は変わりません。生成されたコードは必ずレビューし、通常の DevOps プロセスに沿って作業します。これを踏まえて、チームによるレビューを有効にする前に、ドキュメント、関連ゲーム機能、アクセシビリティ機能の作成に関する GitHub Copilot の提案を確認しましょう。

## セキュリティと GitHub Copilot cloud agent

Copilot cloud agent は非同期かつ人の監視なしにタスクを実行するため、安全性を確保するためのセキュリティ制約があります。

- Copilot はリポジトリに対する読み取りアクセスと、自身の作業ブランチに対する書き込みアクセス **のみ** を持ちます。
- Cloud agent は GitHub Actions 内で実行され、作業用の一時的な別環境を作成します。
- GitHub Actions のワークフローは、実行前に人間の承認が必要です。
- MCP サーバーを含む[外部リソースへのアクセスは、既定で制限されています](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)。

## 生成されたドキュメントをレビューする

まず、GitHub Copilot cloud agent が作成した最初のプルリクエスト (PR) を確認します。コードにドキュメントを追加する PR です。GitHub.com の標準的な PR インターフェイスを使います。

> [!NOTE]
> PR を確認するときに、GitHub Copilot がファイアウォールでブロックされたという警告が表示される場合があります。これは想定どおりです。MCP サーバーへの外部リクエストなど、Copilot の外部リソースへのアクセスは既定で制限されています。必要であれば、[Copilot cloud agent のファイアウォールをカスタマイズまたは無効化][agent-firewall]できます。

1. github.com のリポジトリに戻ります。
2. **Pull Requests** を選択して、プルリクエストの一覧を開きます。
3. **Add missing documentation** などのタイトルが付いたプルリクエストを開きます。

> [!NOTE]
> Copilot がまだ作業中の場合、プルリクエストのタイトルに **[WIP]** が含まれます。その場合は、Copilot が完了するまで待ちます。数分かかることがあるので、休憩したり、これまでに学んだ内容を振り返ったりしてください。

4. PR の準備ができたら **Files changed** タブを選択し、変更をレビューします。

    ![Files changed タブ](../../../_images/shared-pr-files-changed.png)

5. 新たに追加された TSDoc のドキュメント コメントなど、更新されたコードを確認します。変更内容は状況によって異なります。

    変更を確認しながら、TSDoc のドキュメント コメント、TypeScript の規約、コメント ヘッダーを探します。これらは演習 2 の最初に確認したカスタム命令ファイルの内容です。

6. 更新内容をレビューして問題がなければ **Conversation** タブに戻り、下にスクロールします。
7. ワークフローの承認待ちを示す表示を確認します。
8. ワークフローの承認待ちがある場合は、**Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

9. PR の checks セクションにワークフローがキューに入ったことが表示されます。問題がなければ、単一の Astro アプリのプロジェクト チェックが成功します。完了まで数分かかる場合があります。

## GitHub Copilot に変更を依頼する

プルリクエストで Copilot と作業する方法は、一方通行ではありません。チームのほかのメンバーと同じように、プルリクエストのコメントやコードのインライン コメントで Copilot に言及できます。Copilot はコメントを認識し、対応する新しいセッションを開始します。結果は確定的ではないため、必ず特定の文面を指定することはできません。たとえば、次のような変更を依頼できます。

- 各コード ファイルの先頭に、役割を簡潔に説明するコメント ヘッダーを追加する。
- TypeScript と Astro のファイルに TSDoc のドキュメント コメントを追加する。
- Astro アプリの構造を説明する README を作成する。

1. 上記の例や、コードベースで追加してほしいドキュメントについて、**@copilot** をメンションして変更を依頼するコメントを追加します。
2. **View Session** を選択して、Copilot の作業を確認します。Copilot が変更用の新しいセッションを開始します。
3. **Back to pull request** を選択するとプルリクエストに戻れます。

    ![プルリクエストに戻る](../../../_images/ex7-back-to-pr.png)

4. Copilot が変更を完了すると、プルリクエストに新しいコミットが表示されます。
5. **Files changed** タブを選択し、変更をレビューします。

納得できるまで改善を繰り返してください。満足したら、ドラフト PR をレビュー可能な状態に変更して、main ブランチにマージできます。

![レビュー可能な状態に PR を変更する](../../../_images/ex7-ready-for-review.png)

## 関連ゲーム機能をレビューする

ゲーム詳細ページに関連ゲームを表示する issue に対して、Copilot が作成した PR を確認しましょう。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Show related games on the game details page** などのタイトルが付いた PR を選択します。
4. **Files changed** タブを選択して、生成されたコードを確認します。
5. 更新内容をレビューして問題がなければ **Conversation** タブに戻り、下にスクロールします。
6. ワークフローの承認待ちを示す表示を確認します。
7. ワークフローの承認待ちがある場合は、**Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

8. PR の checks セクションにワークフローがキューに入ったことが表示されます。問題がなければ、単一の Astro アプリのプロジェクト チェックが成功します。完了まで数分かかる場合があります。
9. **任意:** 関連ゲーム機能を手動でテストするため、Codespace でこのブランチに切り替えることもできます。Codespace に移動し、ターミナルを開いて、次のコマンドを実行します (`<branch-name>` は Copilot が作成したブランチ名、たとえば **copilot/fix-8** に置き換えます)。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

Copilot が関連ゲーム機能を作成しました。前の作業と同様に、Copilot cloud agent に変更を依頼して反復できます。たとえば、表示する関連ゲームの数を調整したり、コメント ヘッダーや TSDoc のドキュメント コメントを追加したりできます (この issue は命令を更新する**前**に割り当てたことを思い出してください)。**Conversation** タブにコメントを追加すると、Copilot が新しいセッションを開始して対応します。

## アクセシビリティ機能をレビューする

最後に、アクセシビリティ カスタム エージェントを使って実装した機能を確認します。この PR には、演習 3 で依頼したハイ コントラスト モードと、演習 4 の Mission control で追加依頼したライト モードの両方が含まれているはずです。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Add high contrast mode to website** などのタイトルが付いた PR を選択します。

> [!NOTE]
> Copilot がまだ作業中の場合、プルリクエストのタイトルに **[WIP]** が含まれます。その場合は、Copilot が作業を終えるまで数分待ちます。

4. **Files changed** タブを選択して、生成されたコードを確認します。
5. 実装をレビューします。特に、次の点を確認します。
   - モード切り替え用の UI コンポーネント
   - ユーザー設定を保存する local storage の使い方
   - ハイ コントラスト モードとライト モードの CSS またはスタイルの変更
   - アクセシビリティ属性 (ARIA ラベル、キーボード操作など)
   - モード切り替えを管理する JavaScript/TypeScript コード

6. 更新内容をレビューして問題がなければ **Conversation** タブに戻り、下にスクロールします。
7. ワークフローの承認待ちを示す表示を確認します。
8. ワークフローの承認待ちがある場合は、**Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

9. PR の checks セクションにワークフローがキューに入ったことが表示されます。問題がなければ、単一の Astro アプリのプロジェクト チェックが成功します。完了まで数分かかる場合があります。
10. **任意:** Codespace でこのブランチに切り替えて、アクセシビリティ機能を手動でテストできます。Codespace に移動し、ターミナルを開いて、次のコマンドを実行します (`<branch-name>` は Copilot が作成したブランチ名に置き換えます)。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    その後、アプリケーションを起動し、ブラウザーでハイ コントラストとライト モードの切り替えを試します。期待どおりに動作し、ページを再読み込みしても設定が維持されることを確認します。

アクセシビリティ カスタム エージェントが、アクセシビリティのベスト プラクティスに沿った機能実装を Copilot に促したことが分かります。アクセシビリティに関する懸念や改善点があれば、前の PR と同様にコメントで **@copilot** に更新を依頼できます。

## 任意の演習 — さらにタスクを委任する

cloud agent は、バックログの実際の項目を任せて別の作業に集中するときに役立ちます。いくつか issue を作成して Copilot に割り当て、習慣にしましょう。たとえば、次のような作業があります。

- ゲーム詳細ページに支援者向けの関心フォームを作成する。
- ゲーム一覧ページにページネーションを実装する。
- データアクセス ヘルパーに入力検証とエラー処理を追加する。

## まとめ

Cloud agent ハーネスを完了しました。各レッスンを通じて、次のことを行いました。

- **このリポジトリに含まれるカスタム命令ファイルを確認し**、cloud agent の出力に与える影響を確認しました。
- **issue を Copilot cloud agent に割り当て**、環境を設定して計画を立て、非同期に作業する様子を確認しました。
- **カスタム エージェントを作成して使用し**、アクセシビリティのハイ コントラストとライト モードの切り替えを追加しました。
- **Copilot agents ページを Mission control として使い**、作業中のアクセシビリティ セッションを監視して指示を追加しました。
- **cloud agent のプルリクエストをレビューして改善し**、`@copilot` に変更を依頼し、ワークフローを承認しました。

## レビューと次のステップ

Cloud agent ハーネスを完了しました。ここで学んだ内容をさらに深めるには、ほかのハーネスも役立ちます。

- 🖥️ **[VS Code ハーネス](../../vscode/)** — IDE から Copilot Chat の agent mode と MCP 統合を試します。
- 💻 **[CLI ハーネス](../../cli/)** — Plan モードと Autopilot モード、agent skill、カスタム QA エージェント、Playwright MCP、Agent Merge、cloud agent に戻る任意の `/delegate` 連携を使い、ターミナルからレビュー済みの変更を2つリリースします。

自分のリポジトリでは、次のアイデアを試してみましょう。

- リファクタリングやテスト カバレッジの issue を cloud agent に割り当てる。
- 別の分野のカスタム エージェントを作成する。
- 別の技術スタック向けに `copilot-setup-steps.yml` を設定する。

## リソース

- [GitHub Copilot][github-copilot]
- [Copilot agents について][copilot-agents]
- [GitHub issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent のセットアップ ワークフローのベスト プラクティス][cloud-agent-best-practices]
- [Copilot cloud agent のファイアウォールを設定する][agent-firewall]

---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
