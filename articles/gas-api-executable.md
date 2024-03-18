---
title: "GASを外部から実行する"
emoji: "🔖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['gas']
published: false
---

## この記事は
GAS（Google Apps Script）を、外部から実行可能にする方法

## 単純なGASを作成する
単に、文字列を受け取り`hello ${name}`の形式でリターンするだけの関数を作成します。

```javascript
function hello() {
  return "hello"
}
```

## 外部から実行可能にする方法2種類
* Web
* 実行可能API

個々の実現方法をみていく前に、簡単に両者の違いをみていきます。

| 方法 | 関数名 | Head |
| ---- | ---- | ---- |
| Webアプリ | `doGet` or `doPost` | Text |
| API | 任意 | Text |

### Webアプリとして行する

### APIとして実行する

## 参考
https://recruit.gmo.jp/engineer/jisedai/blog/run-gasscript-via-api/
https://zenn.dev/overflow_offers/articles/20220616-google-app-script-technique
https://developers.google.com/apps-script/api/samples/execute?hl=ja
