---
title: 'システムを建設する'
tocTitle: 'アーキテクチャー'
description: 'コンポーネントライブラリーからデザインシステムを抽出する方法'
commit: b696f85
---

第2章では、既存のコンポーネントライブラリからデザインシステムを抽出します。その過程で、どのコンポーネントがデザインシステムに属しているかを判断し、開発者が開始する際に直面する一般的な課題を概説します。

大きな企業でのアーキテクチャーはデザインチーム、エンジニアリングチーム、および製品チームが連携して行わいます。Chroma（Storybookの背後にある会社）とStorybookは、3つ以上のプロパティに渡り800人以上のオープンソース貢献者を提供する、素晴らしいフロントエンドインフラチームを持っているので、プロセスの概要を説明します。

## チャレンジ

もしあなたが開発チームで働いているなら、より大きいチームはあまり効率的ではないことに気づいているでしょう。チームが大きくなるに連れ、ミスコミュニケーションが横行します。既存のUIパターンは文書化されてないか、完全に失われてしまう。つまり、開発者は新しい機能を作る代わりに、既存のものを再開発することになります。プロジェクトには一時的なコンポーネントが散らばってしまいます。

私たちはこの苦境に逢着しました。経験豊富なチームの最善の意図にも関わらず、UIコンポーネントは無限に再構築されるか、コピペされました。外見と機能が同じであるはずのUIパターン。ユニークな各コンポーネントの存在は新しい開発者が正のソースを識別できなくし、貢献度を低下させました。

![UIs diverge](/design-systems-for-developers/design-system-inconsistent-buttons.jpg)

## デザインシステムを作る

デザインシステムは、一般的なUIコンポーネントを、パッケージマネージャーを介して配布される、よく管理された中央のレポジトリに統合します。開発者は、複数のプロジェクトに同じUIコードを貼り付ける代わりに、標準化されたUIコンポーネントをインポートします。

ほとんどのデザインシステムはゼロからスクラッチで構築されていません。代わりに、会社全体で使用されている実証済みのUIコンポーネントから組み立てられ、デザインシステムとして再パッケージ化されます。私たちのプロジェクトも例外ではありません。時間を節約し、デザインシステムをステーク・ホールダーに迅速に提供するために、既存のプロダクトのコンポーネントライブラリからコンポーネントを選択します。

![What's in a design system](/design-systems-for-developers/design-system-contents.jpg)

## デザインシステムはどこに存在するか？

デザインシステムをコンポーネントライブラリと考えることができます。が、1つのアプリにとどまらず、組織全体に役立ちます。プロジェクト独自のコンポーネントライブラリには複合コンポーネントから画面まで何でも含める反面、デザインシステムはプリミティブなUIに焦点を当てます。

そのため、デザインシステムはプロジェクト及びすべてのプロジェクトのDependecyから独立している必要があります。デザインシステムへの変更は、パッケージマネージャーによって配布されるバージョン管理されたパッケージを介して組織全体に伝播します。プロジェクトは、デザインシステムのコンポーネントを再利用し、必要に応じてさらにカスタマイズできます。これらの制約は、フロントエンドプロジェクトを整理するための青写真を提供してくれます。

![Who uses a design system](/design-systems-for-developers/design-system-consumers.jpg)

## create-react-appとGitHubでレポジトリをセットアップする

[State of JS](https://stateofjs.com/)の調査によると、Reactは最も人気のあるビューレイヤーです。圧倒的な数のStorybookがReactを使用しているため、このチュートリアルでは、人気の[create-react-app](https://github.com/facebook/create-react-app)ボイラープレートを使用しています。

```bash
npx create-react-app learnstorybook-design-system
```

<div class="aside">デザインシステムを作成する他の有効な方法には、rawなHTML/CSSの配布、他のビューレイヤーの使用、Svelteでのコンポーネントのコンパイル、またはWebコンポーネントの使用が含まれます。チームに適したものを選択してください。</div>

create-react-appがレポジトリを作成したら、それをGitHub（デザインシステムのコードをホスティングするために使用します）にプッシュできます。サインインして、GitHub.comで新しいレポジトリを作成することから始めます。

![Create a GitHub repository](/design-systems-for-developers/create-github-repository.png)

次に、GitHubの指示に従って、リモートをgitレポジトリに追加し、（まだほとんど空の）レポジトリをプッシュします。

```bash
cd learnstorybook-design-system
git remote add origin https://github.com/chromaui/learnstorybook-design-system.git
git push -u origin master
```

必ず `chromaui` を自分のアカウント名に置き換えてください。

![Initial commit to GitHub repository](/design-systems-for-developers/created-github-repository.png)

## 含むものと含まないもの

デザインシステムは必ず純粋な[Presentationalコンポーネント](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)のみを含むべきです。これらはUIの表示方法を処理し、Propsにのみ反応し、アプリ固有のビジネスロジックを含まず、データのロード方法にとらわれません。これらの要素はコンポーネントを再利用可能にするために不可欠です。

デザインシステムは、組織内のすべてのコンポーネントライブラリのスーパーセットではありません。それを追っていれば頭痛の種になるでしょう。

ビジネスロジックを含むアプリ固有のコンポーネントは、他のプロジェクトに同一のビジネス制約を要求し、再利用を挫折させるため、含めるべきではありません。

現在再利用されていない一時的なコンポーネントは省略します。いつか彼らがデザインシステムの一部になることを望んでも、機敏なチームは可能な限り過剰なコードを維持することを避けます。

## インベントリを作成する

最初のタスクは、コンポーネントのインベントリを作成して、最も使用されているものを特定することです。多くの場合、さまざまなWebサイトまたはアプリの画面を手動でカタログ化して、共通のUIパターンを識別します。[Brad Frost](http://bradfrost.com/blog/post/interface-inventory/) や [Nathan Curtis](https://medium.com/eightshapes-llc/the-component-cut-up-workshop-1378ae110517) などのデザイナーがコンポーネントのインベントリを作成する便利な方法論を公開しているため、このチュートリアルではこれ以上詳しく説明しません。

開発者にとって有用な発見的手法（Heuristics）

- UIパターンを3回以上使用する場合は、再利用可能なUIコンポーネントに変える。
- UIコンポーネントが3つ以上のプロジェクト/チームで使用されている場合、デザインシステムに配置する。

![Contents of a design system](/design-systems-for-developers/design-system-grid.png)

このメソッドに従って、アバター、バッジ、ボタン、チェックボックス、インプット、ラジオ、セレクト、テキストエリア、ハイライト（コード用）、アイコン、リンク、ツールチップなどのUIプリミティブが作成されます。これらのパーツは、クライアントアプリで数え切れないほどのユニークな機能とレイアウトを組み立てるためにさまざまな方法で設定されます。

![Variants in one component](/design-systems-for-developers/design-system-consolidate-into-one-button.jpg)

見つけたコンポーネントをコンピューターにダウンロードしてレポジトリに落とし、Create React Appが提供するアプリケーションファイルを削除して、追加したコンポーネントを追加します。

```bash
rm -rf src/*

svn export https://github.com/chromaui/learnstorybook-design-system/tags/download-1/src
```

<div class="aside">
<p><code>svn</code> (Subversion) を使用して、GitHubからファイルのフォルダーを簡単にダウンロードしました。Subversionがインストールされていない場合、または手動で実行したい場合は、<a href="https://github.com/chromaui/learnstorybook-design-system/tree/download-1/src">ここ</a>から直接フォルダを取得できます。</p>

<p>
コードサンプルでは、​​これらのコンポーネントのサブセットを選択して、レポジトリに関する推論を簡単にしました。一部のチームは、テーブルやフォームなどの他のコンポーネント用に、カスタマイズされたサードパーティコンポーネントをデザインシステムに含めています。
</p></div>

また、コンポーネントが依存するDependenciesを更新する必要があります。

```bash
yarn add prop-types styled-components polished
```

<div class="aside">ここでは<a href="https://www.styled-components.com">styled-components</a>を使用します。これは、CSS-in-JSのライブラリで、コンポーネントにスタイルをスコープするようにするライブラリです。他にも手動でクラスを当てたり、CSSモジュールなどコンポーネントをスタイリングするための有効な方法があります。</div>

UIコンポーネントに加えて、プロジェクト全体で再利用されるTypography、色、余白などのスタイリング定数を含めることは理にかなっています。デザインシステムの命名法で、グローバルスタイル変数を「デザイントークン」と呼びます。このガイドでは、デザイントークンの背後にある理論については詳しく説明しませんが、オンラインでさらに学ぶことができます（こちらの[記事](https://medium.com/eightshapes-llc/tokens-in-design-systems-25dd82d58421)をご覧ください）。

ここからデザイントークンをダウンロードし、レポジトリに追加します。

```bash
svn export https://github.com/chromaui/learnstorybook-design-system/tags/download-2/src/shared src/shared
```

<div class="aside">
<p><a href="https://github.com/chromaui/learnstorybook-design-system/tree/download-2/src/shared">ここ</a>で直接GitHubから直接ファイルをダウンロードすることもできます。</p>
</div>

## 開発を始めましょう

何を構築するかとそれらがどのように適合するかを定義しました。今から仕事に取り掛かります。第3章では、デザインシステムの基本的なツールを構築します。UIコンポーネントのrawなディレクトリを、Storybookの力を借りてカタログ化及び表示できるようにします。