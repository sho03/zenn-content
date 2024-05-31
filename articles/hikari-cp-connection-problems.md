---
title: "HikariCPコネクション問題"
emoji: "🤖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["mysql", "hikaricp", "springboot"]
published: false
---

## この記事は
HikariCPを使用中、以下のようなログに遭遇しました。

1. コネクションが全て利用中となる(`active=50`になる)
```
HikariPool-1 - Timeout failure stats (total=50, active=50, idle=0, waiting=0)
```
2. コネクションが枯渇する（`total=0`になる）
```
HikariPool-1 - Timeout failure stats (total=0, active=0, idle=0, waiting=3)
```
というログが出たので調査しました。

:::details HikariCPログの設定方法
Spring Bootであれば、`application.yml`に以下のような設定を追記することでHikariCPのプールの状況が随時出力されるようになります。
なお、下記ではコネクションリーク時にログを出力する設定も追加しています。
```yml:application.yml
# loggingの設定
logging:
  level:
    com:
      zaxxer:
        hikari: DEBUG
# コネクションリークの設定（30秒）
spring:
  datasource:
    hikari:
      leak-detection-threshold: 30000
      maximum-pool-size: 50
```
:::

:::details 環境など
| ライブラリ | バージョン |
| ---- | ---- |
| Spring Boot |  |
| HikariCP | |

:::

## HikariCPについて
https://github.com/brettwooldridge/HikariCP
Spring Bootがデフォルトで使用するコネクションプールライブラリです。
仮にSpring Bootで利用している場合、起動時に以下のようなログが出力されます。

## 各項目について
### active

### total

### idle

### waiting

## 再現してみる
どういった場合に本事象が再現するのかを調査します。

## 参考
https://github.com/brettwooldridge/HikariCP
https://qiita.com/oohira/items/98bd5bdfe3d6a38e87d4
