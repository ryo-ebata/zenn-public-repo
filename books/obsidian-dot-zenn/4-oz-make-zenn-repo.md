---
title: "保管庫と Zenn リポジトリの作成"
cssclass: zenn
date: 2022-12-22
modified: 2022-12-25
url: "https://zenn.dev/estra/articles/oz-make-zenn-repo"
AutoNoteMover: disable
tags: [" #type/zenn/book #obsidian  "]
aliases: OZ本『保管庫と Zenn リポジトリの作成』
---

# このチャプターについて

このチャプターでは、Obsidian の保管庫に Zenn のリポジトリを作成して運用していく方法について解説していきます。

# 保管庫を作成しよう

リポジトリを作成する前に Obsidian では個人のナレッジベースとなる保管庫 (Valut) をまずは作成する必要があります。保管庫とはフォルダそのものですが、保管庫として Obsidian に登録しておくことでそのフォルダ内部のノートをインデックス化してリンクによるネットワークを構築できるようになります。

Obsidian をインストールして起動すると以下のようなスタートアップ画面が表示されます。

![セットアップモーダル](/images/oz/img_oz-valut-setupmenu.png)

「保管庫を新規作成する」の項目で「作成」ボタンをクリックすると新しいフォルダを作成してそのフォルダを保管庫として扱うように Obsidian に登録できます。

あるいはすでにマークダウンファイルが入ったフォルダがあるなら、「保管庫としてフォルダを開く」の項目から「開く」ボタンをクリックすることで、フォルダの選択画面が開くので、保管庫として扱いたいフォルダを選択することで保管庫を作成できます。

保管庫を作成すると通常の UI 画面が立ち上がります。

![初回起動時画面](/images/oz/img_oz-after-creating-vault.jpg)

「新規ファイルを作成」ボタンをクリックするかホットキーである `Cmd+N` を実行することで新しいマークダウンファイルを作成し、ノートを取り始めることができます。あるいはサイドバー左上のペンマークをクリックすることでもファイル作成が可能です。

:::message
基本操作の練習を行いたい場合には『[記事や本を作成しよう](5-oz-make-article-book)』のチャプターで紹介する「サンドボックス保管庫」を開いてそこで練習するようにしましょう。
:::

# Zenn のリポジトリを作成しよう

Zenn リポジトリの基本的な作成方法と連携方法については以下の Zenn 公式記事で紹介されています。

https://zenn.dev/zenn/articles/connect-to-github

ここでは Obsidian の保管庫内に Zenn リポジトリを配置する方法にフォーカスして解説していきます。

## リポジトリ配置用フォルダの作成

まずはリポジトリを配置するためのディレクトリを保管庫内に作成します。

Zenn に限らず、ブログなどのリポジトリを Obsidian 内部に構築する場合には特定のディレクトリに集約して配置しておくと良いです。これは同期プラグインでのファイル同期と git 管理しているリポジトリの管理を分離しておくためです。

筆者の環境では以下のように SubVault というフォルダを保管庫内に作成して、Zenn や他のブログ用のリポジトリを一括で配置して管理しています。

```sh
❯ exa --tree --level=1 ./SubVault/
./SubVault
├── deno-blog
├── GistDir
├── hugo-repo
├── zenn-private-book
└── zenn-repo
```

Zenn のテンプレートリポジトリなどから GitHub で作成した場合にはこのようなリポジトリ配置用のディレクトリ内に `git clone` してリポジトリのクローンを行ってください。

```sh
❯ git clone [my-zenn-repo]
```

## 同期プラグインを利用している場合にはフォルダを除外しよう

同期プラグインを使っている場合には、上記のリポジトリ配置用のディレクトリを同期から除外するように設定しておきましょう。「コアプラグイン」→「同期」→「選択的同期」→「除外フォルダ」→「管理」から除外したいフォルダ名を設定します。

![img](/images/oz/img_oz-selective-sync.jpg)

これでリポジトリ内のファイルはプラグインによる同期がされないように設定できました。

## npm ではなく pnpm や deno などを使う

リポジトリを用意するための準備ができたので作成していきますが、まず注意点として、なんらかのライブラリやパッケージをリポジトリ内で利用する際には [npm](https://docs.npmjs.com) ではなく [pnpm](https://pnpm.io) や [deno](https://deno.land) などの `node_modules` フォルダをなるべく作る必要のないツールを使うのをおすすめします。

というのも、npm を使ってしまうと深いネストのある `node_modules` フォルダが保管庫内のディレクトリに作成されてしまい、ノートの検索時に利用しない大量のフォルダが無駄にサジェストされたり、「ファイルを別のフォルダに移動」のコマンド実行時に探しようのないネストの深い場所に誤ってノートが飛ばされてしまう可能性があるからです。

執筆環境で利用したい Textlint などのパッケージなら pnpm でも deno でも利用できるので、そちらのツールを使うようにしましょう。

## 新しくローカルから作成する場合

リポジトリのクローンではなく、zenn-cli を使ってローカルでリポジトリを作成する場合には、まずはディレクトリを作成し、`zenn-cli` をインストールします。

```sh
❯ mkdir SubVault
❯ cd ./SubVault
```

`npm init` に相当する `package.json` を作成するプロジェクト初期化コマンド `pnpm init` を実行します。
```sh
❯ pnpm init
```

`npm install` に相当する [pnpm install](https://pnpm.io/ja/cli/install) コマンドで `zenn-cli` をローカルインストールします。

```sh
❯ pnpm install zenn-cli
```

[pnpm exec](https://pnpm.io/cli/exec) コマンドで `npx` の一部に相当するシェルコマンド実行ができるので、以下のコマンドで Zenn リポジトリのセットアップを行いましょう。

```sh
❯ pnpm exec zenn init

  🎉  Done!
  早速コンテンツを作成しましょう

  👇  新しい記事を作成する
  $ zenn new:article

  👇  新しい本を作成する
  $ zenn new:book

  👇  投稿をプレビューする
  $ zenn preview

```

この場合には以下のようなディレクトリ構造が展開されます。

```sh
❯ exa --tree --level=3 -a
.
├── .gitignore
├── articles
│  └── .keep
├── books
│  └── .keep
├── node_modules
│  ├── .bin
│  │  └── zenn
│  ├── .modules.yaml
│  ├── .pnpm
│  │  ├── lock.yaml
│  │  └── zenn-cli@0.1.134
│  └── zenn-cli -> .pnpm/zenn-cli@0.1.134/node_modules/zenn-cli
├── package.json
├── pnpm-lock.yaml
└── README.md
```

`node_modules` フォルダさえ作らずに、使い捨てのプログラムとして実行したい場合にはこれまた `npx` 相当である [pnpm dlx](https://pnpm.io/cli/dlx) コマンドを実行することで可能です。

```sh
❯ pnpm dlx zenn-cli init
.../Library/pnpm/store/v3/tmp/dlx-79668  |   +1 +
.../Library/pnpm/store/v3/tmp/dlx-79668  | Progress: resolved 1, reused 1, downloaded 0, added 1, done

  🎉  Done!
  早速コンテンツを作成しましょう

  👇  新しい記事を作成する
  $ zenn new:article

  👇  新しい本を作成する
  $ zenn new:book

  👇  投稿をプレビューする
  $ zenn preview

```

この場合には以下のような簡素なディレクトリ構造が展開されます。

```sh
❯ exa --tree --level=3 -a
.
├── .gitignore
├── articles
│  └── .keep
├── books
│  └── .keep
└── README.md
```

:::message alert
この場合には VSCode の Zenn プラグインなどが利用できないので注意してください。
:::

リポジトリのセットアップができたら、GitHub の Web ページからリポジトリを作成するか [GitHub CLI](https://cli.github.com) の `gh create repo` コマンドを使ってコマンドラインからリポジトリを作成してください。

```sh
❯ gh repo create zenn-repo --public "Zenn用公開リポジトリ"
```

GitHub のリポジトリを作成したらそれをリモートリポジトリとして設定します。これで `git push` できるようになります。

```sh
❯ git remote add origin https://github.com/user-name/zenn-repo
```

## Textlint を入れよう

あとで紹介する Obsidian Linter プラグインでもフォーマットはできますが、より詳細なリンター/フォーマットルールの設定や校正を行いたい場合には Textlint を開発依存として入れます。

```sh
❯ pnpm install -D textlint
```

:::message
pnpm を利用するとグローバルにある store と呼ばれる場所にパッケージが保存されて、プロジェクト間で使い回されるので、データ量の節約ができます。
:::

Textlint で使用する以下のいくつかのプリセットルールも入れておきましょう。

1. [textlint-ja/textlint-rule-preset-JTF-style: JTF日本語標準スタイルガイド for textlint.](https://github.com/textlint-ja/textlint-rule-preset-JTF-style)
2. [textlint-ja/textlint-rule-preset-ja-spacing: スペース周りのスタイルを扱うtextlintルール集](https://github.com/textlint-ja/textlint-rule-preset-ja-spacing)
3. [textlint-ja/textlint-rule-preset-ja-technical-writing: 技術文書向けのtextlintルールプリセット](https://github.com/textlint-ja/textlint-rule-preset-ja-technical-writing)

```sh
❯ pnpm install -D \
  textlint-rule-preset-ja-spacing \
  textlint-rule-preset-ja-technical-writing \
  textlint-rule-spellcheck-tech-word
```

Textlint の設定用ファイル `.textlintrc` なども作成します。

```sh
❯ pnpm exec textlint --init
```

上記コマンドで設定ファイル `.textlintrc` が作成されるので、プリセットに関してのルールなどを設定すれば、textlint が利用できるようになります。

VSCode 上で Textlint の警告を表示させるには以下のプラグインを利用します。

https://github.com/taichi/vscode-textlint

これで基本的な執筆環境の準備が整いました。

### 注目のプラグイン

Textlint はこれまで Obsidian で直接使うことができませんでしたが、以下の記事で shivase さんが Textlit プラグイン開発を行ってくれているようです。

https://zenn.dev/shivase/articles/011-obsidian-textlint-plugin

コミュニティプラグインとしては公式リリースはまだされていませんが、現在個人的に注目しているプラグインとなっています。
