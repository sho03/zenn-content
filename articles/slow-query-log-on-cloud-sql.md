---
title: "Cloud SQLでスロークエリを検知する"
emoji: "🐙"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["gcp", "mysql"]
published: false
publication_name: "welmo"
---

## この記事でやること
Cloud SQL（MySQL）で稼働しているデータベースのスロークエリをオンにすることで処理に時間がかかるクエリを記録できるようにします。

## Terraformで設定する
[クエリログを有効にする](https://cloud.google.com/sql/docs/mysql/diagnose-issues?hl=ja#query-logs)を参考に、Terraformを利用して[データベースフラグを追加](https://cloud.google.com/sql/docs/mysql/flags?hl=ja#set_a_database_flag)します。
```diff tf:main.tf
resource "google_sql_database_instance" "default" {
  ## 諸々データベースの基本的な設定は省略しています
  settings {
+    database_flags {
+      name  = "log_output"
+      value = "FILE"
+    }
+    database_flags {
+      name  = "slow_query_log"
+      value = "on"
+    }
  }
}
```
