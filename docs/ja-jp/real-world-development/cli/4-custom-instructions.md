---
title: "レッスン 4 - カスタム命令で Copilot を導く"
description: "リポジトリの命令を確認し、ドキュメント標準を追加して、フィルタリングのコードに適用します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

生成 AI を使うときは、コンテキストが重要です。タスクを特定の方法で進める必要があるなら、その指針を Copilot が参照できるようにしましょう。[命令ファイル][instruction-files]では、必要なコードだけでなく、その構成方法も説明できます。フィルタリング機能を実装したので、Copilot が参照した命令を確認し、ドキュメント標準を追加してコードに適用します。

このレッスンでは、次のことを行います。

- リポジトリの命令ファイルと、パスを指定した命令ファイルがエージェントに適用される仕組みを確認します。
- コーディング標準に従うよう、命令ファイルを更新します。
- 命令ファイルがコードに与える影響を確認します。

## シナリオ

Tailspin Toys には、開発手法に関するガイドラインと要件があります。たとえば、次のとおりです。

- コメントはコードの言い換えではなく、意図や自明でない判断を説明します。
- `db/` と `src/lib/` のエクスポートされた関数では、目的、パラメーター、戻り値を TSDoc/JSDoc で説明します。該当する場合は、注入可能な `db` 引数も含めます。
- 再利用可能な Astro コンポーネントでは `Props` の契約を説明し、関連コードが変わったらコメントも更新します。
- 既存のフォーマットと lint のガイダンスを維持します。

命令ファイルを使い、Copilot がこれらの開発手法に沿ってタスクを実行できるようにします。

## 命令ファイル

カスタム命令を使うと、コーディング スタイルや要件に関するコンテキストを Copilot に提供できます。好みの規約、ライブラリ、コメントの書き方などを指定でき、リポジトリ全体に適用する命令と、特定の種類のファイルに限定した命令を作成できます。

命令ファイルには次の2種類があります。

- `.github/copilot-instructions.md`: リポジトリ内のすべてのリクエストで Copilot に送られる単一の命令ファイルです。多くのリクエストに関係するプロジェクト全体の情報を記載します。
- `.github/instructions/*.instructions.md`: 特定の言語、ファイルの種類、タスクに関するガイドラインを記載します。

> [!NOTE]
> ほかの命令形式やサポート状況は、環境によって異なります。特定の形式を前提とする前に、[カスタム命令のサポート状況][custom-instructions-support]を確認してください。

## このプロジェクトのカスタム命令ファイルを確認する

スターター プロジェクトには、すでに命令ファイルが含まれています。変更を加える前に内容を確認し、変更の影響を比べられるようにしましょう。

1. Codespace に戻ります。
2. Codespaces エディターで (ターミナルではなく) `.github/copilot-instructions.md` を開きます。
3. プロジェクトの簡単な説明とコーディング ガイダンスを確認します。この命令は、このリポジトリでの Copilot とのすべてのやりとりに適用されます。
4. `.github/instructions` フォルダーを開き、ファイルを確認します。Astro ファイル、Drizzle データレイヤー、テストなどに関する命令があります。
5. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドで、命令を適用するファイルを決める glob が指定されています。
6. `.github/instructions/drizzle.instructions.md` を開き、ほかの命令ファイルや既存ファイルへのリンクを確認します。大きな命令セットを小さな再利用可能ファイルに分け、Copilot が参考にする例を示せます。

## チームのガイダンスに合わせて命令ファイルを更新する

すでにあるファイルはよい出発点ですが、追加できる内容があります。TypeScript に新しく追加するコードに TSDoc コメントを付けるよう、中心となる `copilot-instructions.md` を変更します。

1. `.github/copilot-instructions.md` の **Code formatting guidance** セクション (35行目付近) を探します。
2. 次の項目を最後に追加します。

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

ファイルは自動的に保存されます。

## 更新したガイダンスを使う

Copilot CLI は会話を始めるときにリポジトリの命令を読み込みます。命令を保存したら、フィルタリングの会話を終了して再開します。こうすることで、機能のコンテキストを保ったまま新しいガイダンスを読み込めます。

1. フィルタリングの会話が開いている場合は、`/exit` を入力して閉じます。Copilot CLI は会話を自動的に保存します。
2. `game-filters-cli` ブランチのリポジトリ ルートから、保存済みの会話を再開します。

   ```bash
   copilot --resume
   ```

   選択を求められた場合はフィルタリングの会話を選び、`game-filters-cli` ブランチがチェックアウトされていることを確認します。
3. 更新したガイダンスを適用するよう Copilot に依頼します。

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. `/diff` を入力し、変更された TypeScript ファイルを確認します。追加された TSDoc コメントの説明がコードと合っていることを確認します。

## まとめと次のステップ

Copilot CLI が命令ファイルからコンテキストを取得する仕組みを確認し、新しい標準を機能に適用しました。このレッスンでは、次のことを行いました。

- リポジトリの命令ファイルとパスを指定した命令ファイルがエージェントに適用される仕組みを確認しました。
- コーディング標準に従うよう、命令ファイルを更新しました。
- 命令ファイルがコードに与える影響を確認しました。

次は、再利用可能な `quality-checks` スキルを[カスタマイズして実行し][next-lesson]、lint とテストが一貫して実行されるようにします。

## リソース

- [Copilot CLI のカスタム命令を追加する][instruction-files]
- [カスタム命令のサポート状況][custom-instructions-support]
- [効果的なカスタム命令を作成するためのベスト プラクティス][instructions-best-practices]
- [Awesome Copilot — 命令ファイルなどのリソース集][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
