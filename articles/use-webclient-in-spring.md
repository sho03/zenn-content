---
title: "SpringでWebClientを使用する"
emoji: "⛳"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Spring"]
published: false
---

# この記事は
普段APIを実行するときに`RestTemplate`を使用していたのですが、[ドキュメント](https://spring.pleiades.io/spring-framework/docs/current/reference/html/integration.html#rest-resttemplate)に
>RestTemplate はメンテナンスモードであり、マイナーな変更とバグのリクエストのみが受け入れられます。代わりに WebClient の使用を検討してください。

という記載を発見したため、`WebClient`でどう書くかを調べてみました。

# 基本的な使い方
