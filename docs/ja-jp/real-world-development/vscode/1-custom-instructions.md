---
title: "演習 1 - カスタム命令 (VS Code)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: Agent mode →][next-lesson]

生成 AI を使うときは、コンテキストが重要です。タスクを特定の方法で進める必要がある場合や、Copilot に背景情報を伝える必要がある場合は、そのコンテキストを参照できるようにしましょう。Copilot に具体的なコンテキストを伝える方法はいくつかあります。特に重要なのが[命令ファイル][instruction-files]です。コード生成に関するガイダンスを提供します。

この演習では、次のことを行います。

- リポジトリのカスタム命令と、パスを指定した命令ファイルを使って、プロジェクト固有のコンテキスト、コーディング ガイドライン、ドキュメント標準が Copilot に伝わる仕組みを確認します。
- 現在の命令を使って、フィルタリング用の最初のデータ スライス (パブリッシャー ヘルパー) を作成します。
- `.github/copilot-instructions.md` にリポジトリ全体の新しい標準を追加します。
- 同じプロンプトを再実行し、生成コードが新しい標準に従うことを確認します。
- 命令の変更とフィルタリング スライスを `main` にコミットし、次の演習で Copilot が最新のガイダンスを使えるようにします。

> [!CAUTION]
> 生成コードが設定した標準から外れる場合があります。Copilot の動作は確定的ではありません。この演習では、更新後の命令によって傾向がどう変化するかを確認します。出力が文字単位で同じになる必要はありません。

## 命令ファイル

### シナリオ

Tailspin Toys には、開発手法に関するガイドラインと要件があります。たとえば、次のとおりです。

- データレイヤーには、必ずユニット テストを用意します。
- UI はダーク モードにし、現代的な見た目にします。
- TSDoc のドキュメント コメントでコードを文書化します。
- 各ファイルの先頭に、ファイルの役割を説明するコメント ブロックを追加します。

命令ファイルを使い、Copilot がこれらの開発手法に沿ってタスクを実行できるようにします。

### カスタム命令

カスタム命令を使うと、コーディング スタイルや要件に関するコンテキストを Copilot に提供できます。好みの規約、ライブラリ、コメントの書き方などを指定でき、リポジトリ全体に適用する命令と、特定の種類のファイルに限定した命令を作成できます。

命令ファイルには次の2種類があります。

- `.github/copilot-instructions.md`: リポジトリ内のすべてのリクエストで Copilot に送られる単一の命令ファイルです。Copilot に送る多くのリクエストに関係する、プロジェクト全体の情報を記載します。技術スタック、開発対象の概要、ベスト プラクティス、全般的なガイダンスなどが含まれます。
- `.github/instructions/*.instructions.md`: 特定のタスクやファイルの種類に対応する命令ファイルです。TypeScript や Astro などの言語、UI コンポーネントの作成やユニット テストなどのタスクに関するガイダンスを記載できます。

> [!NOTE]
> IDE では、命令ファイルは Copilot Chat でのコード生成にのみ使われ、コード補完や次の編集候補には使われません。
>
> Copilot Chat、Copilot CLI、Copilot cloud agent は、コード生成時にリポジトリ レベルの命令と、`applyTo` frontmatter を持つ `*.instructions.md` ファイルの両方を使います。
>
> Copilot は AGENTS.md や CLAUDE.md など、[ほかの標準形式の命令ファイル][custom-instructions-support]もサポートしています。

### 命令ファイルを管理するためのベスト プラクティス

命令ファイルの作成方法を詳しく扱うには、このワークショップでは範囲が広すぎます。ただし、サンプル プロジェクトには代表的な方法が含まれています。基本的には、次のようにします。

- `copilot-instructions.md` には、開発対象、プロジェクトの構造、全般的なコーディング標準など、プロジェクト全体に関する情報を記載します。
- `*.instructions.md` には、ファイルの種類 (ユニット テスト、Astro コンポーネント、データレイヤー) やタスクに特化した命令を記載します。
- 自然な言葉を使い、明確なガイダンスと、コードのよい例・悪い例を示します。

命令ファイルに唯一の正解はありません。AI の使い方と同じように、プロジェクトに合った方法を試して見つけましょう。

> [!TIP]
> GitHub Copilot を使うプロジェクトには、充実した命令ファイルを用意することをお勧めします。このプロジェクトでは、[UI 更新][ui-instructions]や [Astro][astro-instructions]など、さまざまなタスク用のファイルを確認できます。
>
> Copilot に命令ファイルを作成してもらうこともできます。手順は環境によって異なります (VS Code の **Configure Chat → Generate Agent Instructions**、Copilot CLI の `/init` など)。現在の環境に関係するレッスンで手順を説明します。
>
> テンプレートや出発点を探す場合は、命令ファイル、カスタム エージェントなどがそろった [awesome-copilot][awesome-copilot] リポジトリを確認してください。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## このプロジェクトのカスタム命令ファイルを確認する

このリポジトリに含まれる命令ファイルを読んでみましょう。中心となる `copilot-instructions.md` と、さまざまなタスク用の `*.instructions.md` ファイルがあります。エディターまたは GitHub の Web UI で開いてください。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの簡単な説明と、**Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** の下には **GitHub Actions Workflows** のガイダンスもあります。これらは、Copilot とのあらゆるやりとりに適用されます。
3. `.github/instructions` フォルダーを開き、Astro ファイル、Drizzle データレイヤー、テストなどに関する命令を確認します。
4. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドでは、リポジトリ ルートからの相対 glob を指定します。たとえば `**/*.test.ts` に一致する TypeScript のテスト ファイルに命令が適用されます。
5. このプロジェクトのユニット テスト作成に関する命令を確認します。
6. `.github/instructions/drizzle.instructions.md` を開き、末尾にある `unit-tests.instructions.md` などの命令ファイルや、プロジェクト内の既存ファイルへのリンクを確認します。大きな命令セットを小さな再利用可能ファイルに分け、Copilot が参考にする例を示せます (パスはリポジトリ ルートではなく、命令ファイルからの相対パスです)。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションにはプロジェクトのコーディング標準が記載されていますが、コード内のドキュメント作成はまだ必須ではありません。次の手順では、TSDoc のドキュメント コメントとファイル コメントのヘッダーに関する規則を追加します。

## 変更用ブランチを作成する

ベスト プラクティスに従い、変更用ブランチを作成します。

1. 前の演習で使った Codespace に戻ります。
2. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して新しいターミナルを開きます。
3. 新しいブランチを作成して切り替えます。

   ```bash
   git checkout -b custom-instructions
   ```

## 命令を更新する前に Copilot Chat を使う

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に Codespace に戻り、Copilot Chat パネルを開き、**New Chat** を選択して新しい会話を始めます。モードとモデルの選択は演習によって異なります。必要な場合は手順で説明します。

カスタム命令の影響を確認するため、まず現在の命令ファイルを使ってプロンプトを送信します。その後、命令を更新し、同じプロンプトを再送信して違いを確認します。

1. 前の演習で開いたエディター タブを閉じ、Copilot に必要なコンテキストだけを渡します。
2. `src/lib/publishers.ts` を開き、ヘルパーの配置先を Copilot に示します。
3. Chat ビューのエージェント ドロップダウンから **Agent** を選択し、Copilot にファイルを変更できるようにします。

   ![Chat ビューのエージェント ピッカーを示すスクリーンショット。](../../../_images/shared-chat-mode-selector.png)

4. 次のプロンプトを送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot はプロジェクトを調べてコードを更新します。多くの場合、ヘルパー ファイルとテストの両方を変更します。
6. 提案されたヘルパーが、`db` クライアントを最初の引数として受け取り、型付きのパブリッシャー配列を返す関数であることを確認します。これは `src/lib/*.ts` に適用される `.github/instructions/drizzle.instructions.md` のデータレイヤー規約に基づいています。
7. 提案されたコードには、TSDoc のドキュメント コメントとファイル レベルのコメント ヘッダーが**ない**ことを確認します。

> [!CAUTION]
> Copilot の動作は確定的ではないため、指示がなくてもドキュメント コメントを追加する可能性があります。その場合も問題ありません。命令を追加した後に動作の一貫性が高まることが重要です。

## リポジトリ標準を追加する

前の説明のとおり、`.github/copilot-instructions.md` は Copilot にプロジェクト全体の情報を提供します。コードの提案を改善するために、リポジトリのコーディング標準を記述しましょう。

1. `.github/copilot-instructions.md` を開き直します。
2. 27行目付近にある **Code formatting requirements** セクションを探します。現在のコーディング標準が記載されていますが、コード内ドキュメントの規則がありません。そのため、生成されたヘルパーにドキュメント コメントがありませんでした。
3. 既存の標準の直後に、次の Markdown を追加して、ファイル コメント ヘッダーと TSDoc コメントを追加するよう Copilot に指示します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 前のレッスンで確認したように、リポジトリ レベル (`.github/copilot-instructions.md`) には全般的な命令を、言語やファイルの種類、タスクに特化した命令には `*.instructions.md` を使えます。今回追加したドキュメント コメントの規則など、プロジェクト全体の標準はリポジトリ レベルのファイルに記載します。

## プロンプトを再実行して変化を確認する

1. Copilot Chat に戻り、**New Chat** を選択してバッファーをクリアします。
2. `src/lib/publishers.ts` をもう一度開き、Copilot に対象ファイルを示します。
3. 前と**同じプロンプト**を送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 今回は、提案されたファイルの先頭に次のようなコメント ブロックがあることを確認します。

   ```typescript
   /**
    * Publisher data-access helpers for the Tailspin Toys Crowd Funding platform.
    * Provides functions to retrieve publisher information from the database.
    */
   ```

5. 提案された関数に次のような TSDoc コメントが含まれることを確認します。

   ```typescript
   /**
    * Returns a list of all publishers with their id and name.
    *
    * @param db - The Drizzle database client.
    * @returns A promise that resolves to an array of publisher objects.
    */
   ```

新しいプロジェクト標準を Copilot に伝え、次の演習で基礎として使うコードに適用できました。

## 命令をコミットしてブランチをプッシュする

命令ファイルもほかのアセットと同じように、ソース管理で管理します。リポジトリにブランチをコミットしてプッシュしましょう。

1. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、新しいターミナル ウィンドウを開きます。
2. ターミナルで次のコマンドを実行し、命令の更新とヘルパーの変更が存在することを確認します。

   ```bash
   git status
   ```

3. ターミナルで命令の更新と、ヘルパーの基礎部分について Copilot が変更したすべてのファイルをステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # git status に追加の更新 (テストなど) が表示される場合は、そのファイルも追加します。
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. ターミナルでブランチをリポジトリにプッシュします。

   ```bash
   git push -u origin custom-instructions
   ```

## プルリクエストを作成してマージする

ブランチをプッシュしたら、バックログにあるドキュメント標準の issue に関連付けてプルリクエストを作成します。手動で行う代わりに、すでにプロジェクトに接続されている GitHub ツールを使って Copilot に依頼できます。issue を見つけてクローズする PR を作成し、マージするよう Copilot に依頼します。

> [!NOTE]
> この演習では、Copilot が初めてあなたの代わりに GitHub を操作します。プロジェクト テンプレートでは GitHub のツールがあらかじめワークスペースに接続されているため、セットアップは不要です。Copilot が初めてツールを使うときに、VS Code で GitHub へのサインインを求められることがあります。画面の指示に従って認証します。この接続 (Model Context Protocol) については、後の演習で学びます。

1. Codespace で Copilot Chat を開きます。
2. <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd> (Windows/Linux) を押して Copilot Chat ビューを開き、エージェント ピッカーで **Agent** が選択されていることを確認します。
3. 命令ファイルの更新に関する issue を検索し、現在のブランチから PR を作成するよう Copilot に依頼します。命令の更新と、これから行うフィルタリング作業の基礎となるパブリッシャー ヘルパーの追加の両方を PR に記載します。

   ```
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot が GitHub 操作を許可するよう求めたら、コマンドを確認して、適切な場合に **Allow** を選択します。
5. PR が作成されたら、PR をマージしてローカル ブランチを `main` に戻すよう、次のプロンプトで依頼します。

   ```
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

6. Copilot が GitHub 操作とシェル コマンドを許可するよう求めたら、コマンドを確認して、適切な場合に **Allow** を選択します。

これで GitHub Copilot の支援を受けてプルリクエストを作成し、マージできました。

## まとめと次のステップ

このプロジェクトの命令ファイルから Copilot がコンテキストを読み取る仕組みを確認し、VS Code の Copilot Chat を使って次のことを行いました。

- 既存の命令でコードを生成するプロンプトを送り、Copilot の出力を確認しました。
- `.github/copilot-instructions.md` にリポジトリ全体の新しい標準を追加しました。
- 同じプロンプトを再実行し、生成コードに新しい標準が反映されることを確認しました。
- 命令の変更とヘルパーの基礎部分を `main` にコミットし、次の演習で使えるようにしました。

次は[agent mode][next-lesson]で、Copilot がコードベース全体に新機能を追加するときに、命令を活用します。

## リソース

- [GitHub Copilot のカスタマイズに使う命令ファイル][instruction-files]
- [カスタム命令を作成するためのベスト プラクティス][instructions-best-practices]
- [Copilot のカスタム命令をよりよく書くための5つのヒント][copilot-instructions-five-tips]
- [GitHub Copilot の個人用カスタム命令][personal-instructions]
- [Awesome Copilot — 命令ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
