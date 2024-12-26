---
title: "CloudRunからCloudSQLへ接続する"
emoji: "🙆"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['gcp', 'springboot']
published: false
---

## この記事は
Cloud Run上で動くSpring Bootアプリケーションから、Cloud SQLへ接続する方法です。

```yml:application.yml
spring:
  datasource:
    hikari:
      maximum-pool-size: 50
    url: jdbc:mysql:///${DB_MAIN_DATABASE}?useUnicode=true&characterEncoding=utf8&useSSL=false&socketFactory=com.google.cloud.sql.mysql.SocketFactory&cloudSqlInstance=${DB_INSTANCE}
logging:
  level:
    com:
      zaxxer:
        hikari: DEBUG
```

```gradle:build.gradle
implementation("com.google.cloud.sql:mysql-socket-factory-connector-j-8:1.16.0")
```

## 参考
https://github.com/GoogleCloudPlatform/cloud-sql-jdbc-socket-factory
https://cloud.google.com/run/docs/authenticating/public?hl=ja#terraform
