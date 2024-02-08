---
title: "Prettierを入れる"
emoji: "🕌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["pretteir", "react"]
published: false
---

## この記事は
`Prettier`を入れた際のメモ書き。なお、すでに`eslint`は導入済みです。

## PrettierとLinter
https://prettier.io/docs/en/comparison

## インストール

### パッケージインストール
[PrettierのInstallページ](https://prettier.io/docs/en/install)に則って行います。

```shell
yarn add --dev --exact prettier
```
また、eslintとの競合を避けるために[`eslint-config-prettier`](https://github.com/prettier/eslint-config-prettier#installation)も入れます。
（[Integrating with Linters](https://prettier.io/docs/en/integrating-with-linters.html)も参照）
```shell
yarn add --dev eslint-config-prettier
```

### .prettierrcを作成する
```shell
node --eval "fs.writeFileSync('.prettierrc','{}\n')"
```
なお、[Configuration File](https://prettier.io/docs/en/configuration)によればファイル名は必ずしも`.prettierrc`である必要はないようです。プロジェクトごとに合わせてみてください。
設定できるオプションについては[Options](https://prettier.io/docs/en/options.html)を参考

### yarnスクリプトから実行できるようにする
```diff json:package.json
"scripts": {
    // yarn formatでprettierが起動できるようにする
+   "format": "prettier --write ."
},
```
他に、`prettier --check .`（上書きせずに結果がだけ出力する）などもあります。`yarn prettier --help`などで確認ください。

## VSCodeとの連携
https://github.com/prettier/prettier-vscode

## Gitとの連携
[Git hooks](https://prettier.io/docs/en/install#git-hooks)を参照

## 設定中にハマったところ
### `yarn lint`と`yarn format`の齟齬
`yarn format`後に`yarn lint`を実行するとlintエラーとなってしまいました。メッセージを見ると、タブのサイズがおかしい模様です。
該当のコードを見てみると、確かにインデントが設定している値（4）とずれていました。
https://github.com/prettier/prettier/issues/1271

## 他参考記事
https://zenn.dev/rescuenow/articles/c07dd571dfe10f