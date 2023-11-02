---
title: "MySQL ver5.7 から 8.0にあげる"
emoji: "😽"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["mysql"]
published: false
---

# この記事は
MySQLを5.7から8.0にあげる際に遭遇したエラーについてのメモ。ローカル環境にてDockerで定義していたイメージのバージョンアップと、GCPのCloudSQL上で稼働しているDBのバージョンアップに対するもの。

# docker-compose.ymlの修正
```diff yaml
version: '3'
services:
  db:
+    image: mysql:8.0
-    image: mysql:5.7.29
```

# 遭遇したエラーなど
## ERROR 1064 (42000) at line 2: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'IDENTIFIED BY 'password'' at line 1
ローカルで発生。
[Grant文のページ](https://dev.mysql.com/doc/refman/5.7/en/grant.html)に以下の記載がある。
>However, use of GRANT to create accounts or define nonprivilege characteristics is deprecated in MySQL 5.7. Instead, perform these tasks using CREATE USER or ALTER USER.

## Public Key Retrieval is not allowed
```
SQL State  : 08001
Error Code : 0
Message    : Public Key Retrieval is not allowed
```
ローカルで発生。
`allowPublicKeyRetrieval=true`を追加する
```diff yaml
+ spring.datasource.url=jdbc:mysql://${env.DB_HOST}:${env.DB_PORT}/${env.DB_MAIN_DATABASE}?useUnicode=true&characterEncoding=utf8&useSSL=false&allowPublicKeyRetrieval=true
- spring.datasource.url=jdbc:mysql://${env.DB_HOST}:${env.DB_PORT}/${env.DB_MAIN_DATABASE}?useUnicode=true&characterEncoding=utf8&useSSL=false
```

[connector-jのドキュメント](https://dev.mysql.com/doc/connector-j/8.1/en/connector-j-connp-props-security.html)を見ると、そもそも`useSSL`が非推奨になり、代わりに`sslMode`をするようになっている模様

:::allowPublicKeyRetrieval
Allows special handshake round-trip to get an RSA public key directly from server.
:::

:::details useSSL
DEPRECATED: See 'sslMode' property description for details.

For 8.0.12 and earlier: Use SSL when communicating with the server, default is "true" when connecting to MySQL 5.5.45+, 5.6.26+ or 5.7.6+, otherwise default is "false".

For 8.0.13 and later: Default is "true".
:::

:::details sslMode
By default, network connections are SSL encrypted; this property permits secure connections to be turned off, or a different levels of security to be chosen. The following values are allowed: "DISABLED" - Establish unencrypted connections; "PREFERRED" - Establish encrypted connections if the server enabled them, otherwise fall back to unencrypted connections; "REQUIRED" - Establish secure connections if the server enabled them, fail otherwise; "VERIFY_CA" - Like "REQUIRED" but additionally verify the server TLS certificate against the configured Certificate Authority (CA) certificates; "VERIFY_IDENTITY" - Like "VERIFY_CA", but additionally verify that the server certificate matches the host to which the connection is attempted.

This property replaced the deprecated legacy properties 'useSSL', 'requireSSL', and 'verifyServerCertificate', which are still accepted but translated into a value for 'sslMode' if 'sslMode' is not explicitly set: "useSSL=false" is translated to "sslMode=DISABLED"; {"useSSL=true", "requireSSL=false", "verifyServerCertificate=false"} is translated to "sslMode=PREFERRED"; {"useSSL=true", "requireSSL=true", "verifyServerCertificate=false"} is translated to "sslMode=REQUIRED"; {"useSSL=true", "verifyServerCertificate=true"} is translated to "sslMode=VERIFY_CA". There is no equivalent legacy settings for "sslMode=VERIFY_IDENTITY". Note that, for all server versions, the default setting of 'sslMode' is "PREFERRED", and it is equivalent to the legacy settings of "useSSL=true", "requireSSL=false", and "verifyServerCertificate=false", which are different from their default settings for Connector/J 8.0.12 and earlier in some situations. Applications that continue to use the legacy properties and rely on their old default settings should be reviewed.

The legacy properties are ignored if 'sslMode' is set explicitly. If none of 'sslMode' or 'useSSL' is set explicitly, the default setting of "sslMode=PREFERRED" applies.
:::

# Cloud SQLのバージョンアップ
https://cloud.google.com/sql/docs/mysql/upgrade-major-db-version-inplace?_ga=2.163791679.-1885893345.1697100412&hl=ja#upgrade-backups