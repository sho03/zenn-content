---
title: "Spring BootのログをJSONにすることでGCP Loggingを見やすくする"
emoji: "🎉"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["logback", "springboot", "gcp"]
published: false
---

# やること
`Spring Boot`のログを[構造化ログ](https://cloud.google.com/logging/docs/structured-logging?hl=ja)にすることで、GCPのロギングをいい感じにします。具体的には以下のようになります。

TODO 画像を入れる

# 

# 環境など

# 方法
`src/main/resources`に`logback-spring.xml`を追加します。

```xml:logback-spring.xml

```

# 変更前後
適当なコントローラクラスにログを仕込んで試してみます。

# ローカル環境では通常のログのまま出力する
変更自体は上記に記載した通りで実現できますが、JSON形式の出力のままだとローカルでのターミナル状のログが若干見にくくなります。
そこで、環境ごとにログの出力内容を変更させます。


# 終わりに
どういったログを出力するのが運用上望ましいかについては以下を参考に。

