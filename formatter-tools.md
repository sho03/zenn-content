---
title: "Prettier/ESLint/lint-staged/husky"
emoji: "😎"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['prettier', 'javascript', 'eslint']
published: false
---
## この記事は
https://prettier.io/

## Prettier
```
npm install --save-dev --save-exact prettier
npm install eslint-config-prettier
```

[ESLint (and other linters)](https://prettier.io/docs/en/install#eslint-and-other-linters)に従い、`eslint-config-prettier`を併せてインストールすることでESLintとのルールのコンフリクトなどを避けます。

`prettier`のルールを記述するためのファイルを作成します。
```
node --eval "fs.writeFileSync('.prettierrc','{}\n')"
```
`.prettierignore`を作成し、フォーマットの除外ファイルを選択します。
```txt:prettierignore
build
coverage
```

## ESLint
#### eslint plugins
#### eslint-plugin-import

#### eslint-plugin-sonarjs

#### eslint-plugin-unused-imports

## lint-staged

## husky

## 参考
