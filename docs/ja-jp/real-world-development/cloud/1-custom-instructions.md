---
title: "演習 1 - カスタム命令 (Cloud agent)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: Copilot cloud agent →][next-lesson]

技術的負債に悩んでいない組織は、ほとんどありません。未解決のセキュリティ問題、更新が必要なレガシー コード、実装する時間がなくバックログに残った機能要求などがあります。GitHub Copilot の cloud agent は、コードの更新や機能の追加といったタスクを自律的に実行できます。作業が終わると、人間の開発者がレビューできるドラフト PR を作成します。面倒な作業を任せることで、開発プロセスを速め、開発者はより大きな課題に集中できます。

この演習では、Copilot cloud agent を使って次のことを行います。

- コードを生成する環境をカスタマイズする。
- 操作を安全に行う。
- 範囲を明確に定めた issue の重要性を理解する。
- issue を Copilot に割り当てる。

> [!NOTE]
> VS Code および CLI のハーネスとは異なり、この演習ではプロンプトの実行前後を比較しません。Copilot cloud agent は GitHub issue に対して非同期に動作するため、リアルタイムで並べて効果を示すのは難しいからです。cloud agent が作成するプルリクエストをこのハーネスの後半でレビューするときに、命令ファイルの影響を確認します。

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

- `.github/copilot-instructions.md`: リポジトリ内のすべてのリクエストで Copilot に送られる単一の命令ファイルです。Copilot に送る多くのチャットや CLI リクエストに関係する、プロジェクト全体の情報を記載します。技術スタック、開発対象の概要、ベスト プラクティス、全般的なガイダンスなどが含まれます。
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

## リポジトリ標準を追加する

ここで行う編集は、コード内のドキュメントを TSDoc コメントとファイル レベルのコメント ヘッダーで記述するプロジェクト全体の規則を追加することです。後続の演習で issue を割り当てるときに、cloud agent がこの規則を読み取ります。

編集する前に、作業用のブランチを作成します (cloud agent は issue の対象ブランチから命令を読み込みますが、変更を明確にコミットしておきます)。

1. Codespace のターミナルで新しいブランチを作成して切り替えます。

   ```bash
   git checkout -b custom-instructions-cloud
   ```

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

## 命令の変更をコミット、プッシュ、マージする

cloud agent は issue の対象ブランチから命令ファイルを読み込みます。次の演習で Copilot に issue を割り当てるとき、Copilot は `main` からブランチを作成します。cloud agent が命令を参照できるよう、変更を `main` にマージする必要があります。

1. ステージングしてコミットします。

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. ブランチをプッシュします。

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. github.com で `custom-instructions-cloud` から `main` へのプルリクエストを開いてマージします。次の演習で Copilot に割り当てる issue に取り組むとき、cloud agent はこれらの命令を読み込みます。

> [!TIP]
> このワークショップでは、`main` に直接作業してもかまいません。その場合はブランチを作成せず、直接 `main` にコミットできます。ここでは実際のプロジェクトに近い手順を示すため、ブランチを使っています。

## まとめと次のステップ

Copilot がこのプロジェクトの命令ファイルからコンテキストを読み取る仕組みを確認し、リポジトリ全体の新しい標準を `.github/copilot-instructions.md` に追加しました。このハーネスの後半で、cloud agent が作成するプルリクエストをレビューするときに、この標準が反映されていることを確認します。

次は、最初の issue を Copilot cloud agent に[割り当てます][next-lesson]。

## リソース

- [GitHub Copilot のカスタマイズに使う命令ファイル][instruction-files]
- [カスタム命令を作成するためのベスト プラクティス][instructions-best-practices]
- [Copilot のカスタム命令をよりよく書くための5つのヒント][copilot-instructions-five-tips]
- [Awesome Copilot — 命令ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
