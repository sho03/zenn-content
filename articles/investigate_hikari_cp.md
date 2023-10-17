---
title: "Hikari CPを調査する"
emoji: "📑"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["hikaricp"]
published: false
---

# 概要
`Spring Boot` + `HikariCP`を利用したアプリケーションを運用していたところ、以下の事象が発生し、DBアクセスエラーが発生した。
```
HikariPool-1 - Pool stats (total=0, active=0, idle=0, waiting=0) 
```
上記のログは、コネクションプールに保持されるはずのコネクションが1つもないことを示している。

# 調査
ログを具に見てみると、コネクションの追加ができていない + `maxLifetime`による接続クローズ、という2つの事象から発生している模様。
```
HikariPool-1 - Add connection elided, waiting=0, adders pending/running=17
HikariPool-1 - Closing connection com.mysql.cj.jdbc.ConnectionImpl@4aabc12e: (connection has passed maxLifetime)
```

# 参考
https://github.com/brettwooldridge/HikariCP/issues/1710