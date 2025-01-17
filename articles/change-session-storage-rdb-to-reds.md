---
title: "HTTPSessionの保存先をMySQLからRedisに変更する"
emoji: "🎃"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["spring"]
published: false
---
## この記事は
MySQLに保存していたセッション情報をRedisに変更します。

## 結論
以下のように、依存関係を修正します。
```diff kts:build.gradle.kts
+ implementation("org.springframework.session:spring-session-data-redis")
- implementation("org.springframework.session:spring-session-jdbc")
```

## 仕組み
依存関係を変更するだけで保存先も変わる仕組みをついでに調査します。

## RDB vs Redis
セッションを保存するのにRedisの方が優れている理由についてです。


## 参考
