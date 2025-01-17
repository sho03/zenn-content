---
title: "SpringでRestクライアントを使う"
emoji: "📘"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['spring']
published: false
---
## この記事は
いつの間にかSprinが提供しているRestクライアントが増えていたのでまとめることにしました。
https://spring.pleiades.io/spring-framework/reference/integration/rest-clients.html

## 結論
個人的にはこんな感じかと思います。
| クライアント | 所感 | 追加バージョン |
| ---- | ---- | ---- |
| `WebClient` | まずはこれを使うことを考える。非同期・ノンブロッキングであることを謳っているが、同期的にも使用できるので、`RestClient`の代替としても利用できる。 | Text |
| `RestClient` | 同期的なRestクライアント。`RestTemplate`の代替としてモダンな機能を取り入れたもの。`WebClient`の同期処理バージョンと考えればよい。 | Text |
| `RestTemplate` | 基本的には使わない。少なくとも新規開発においては選択肢に入れなくて良い。現時点では`deprecated`になっているわけでもないので、急いで上記のクライアントに変更しなくても問題ない。ただし、新規機能が追加されないことは留意すること。 | Text |

## 実装例
`WebClient`と`RestClient`の使い方を簡単に説明します。まずは両者のコードを見てみましょう。
`RestTemplate`については上記の理由から実装からは外しています。他の記事を参考にしてください。
```kotlin:sample.kt

```

### WebClient
`WebClient`を使用した際に注目すべきは戻り値の型が`Mono`もしくは`Flux`になることでしょう。

### RestClient

## コンポーネントに切り出す
ここからは余談ですが、`WebClient`や`RestClient`

## 非同期・ノンブロッキング

## 参考
https://www.baeldung.com/spring-webclient-resttemplate
https://spring.pleiades.io/spring-framework/reference/integration/rest-clients.html
