---
title: "@Transactionalアノテーションをつけるとなんでトランザクションが発生するんですか？から始めるSpringの動作原理"
emoji: "🐡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['spring', 'kotlin']
published: false
---
## この記事は
`@Transactional`をつけるとそのメソッドはトランザクション境界が発生することはSpringを利用している方は知っていることと思います。
本記事では`@Transactional`をつけることでどのようにトランザクション境界を発生させているかの仕組みを解説していきます。

### 対象読者
* タイトルのような疑問を抱えている方
* Java初学者
* Proxy/リフレクションに詳しくない方

## まとめ
* Proxyを使ってコンポーネントのサブクラスを生成しています
* リフレクションを使って`@Transactional`がついたメソッドを持つクラスを識別してます

## Proxy

## リフレクション

## @Transactionalの動作外観

## 処理を追ってみる(デバッグする)
`@Transactional`がついた処理が実際にどのようなコードフローになるかを見てみましょう。

## 参考
