---
title: "Cloud SQLでスロークエリを検知する"
emoji: "🐙"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["gcp", "mysql"]
published: true
publication_name: "welmo"
---

## この記事でやること
Cloud SQL（MySQL）で稼働しているデータベースのスロークエリをオンにすることで処理に時間がかかるクエリを記録できるようにします。

具体的には以下の設定を行います。

* Cloud SQLのスロークエリの設定
* Cloud Loggingのログポリシーアラートの設定

## スロークエリログを設定する
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
`terraform apply`に成功すると、Cloud SQLのコンソール画面で`log_output`と`slow_query_log`フラグが設定されていることが確認できます。
![](https://storage.googleapis.com/zenn-user-upload/ff72a606cc87-20240301.png)
なお、「スロークエリ」に該当するのは[`long_query_time`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_long_query_time)を超えた場合です。デフォルトでは10秒が設定されています。（`long_query_time`も同様にTerraformで設定可能です。）

### 試してみる
スロークエリを意図的に発生させるため、`sleep`を利用してロギングされるか確認します。
```sql
select sleep(11);
```
ログを確認すると、スロークエリが記録されていることがわかります。（一部マスクしてます）
```
# Time: 2024-03-01T01:50:55.879244Z
# User@Host: xxxx[xxxx] @  [xxxxxxxxxxx]  thread_id: 2146446  server_id: 1359838164
# Query_time: 11.001692  Lock_time: 0.000000 Rows_sent: 1  Rows_examined: 1
use database_main;
SET timestamp=1709257844;
select sleep(11);
```

## スロークエリを検知する（Slack通知）
上記までではスロークエリがログに残るだけですので、素早く検知するためにSlackへの通知もできるようにします。
なお、Slack以外にもメール等への通知も可能です。（[通知チャンネルの作成と管理](https://cloud.google.com/monitoring/support/notification-options?hl=ja#slack)）

[ログベースの指標に関するアラート](https://cloud.google.com/logging/docs/logs-based-metrics/charts-and-alerts?hl=ja)を参考に設定します。
ログの条件には以下のように設定しました。
```
resource.type="cloudsql_database"
insertId=~".*slow.*"
```
::::details insertIdについて
:::message
`insertId`については、スロークエリログに共通して出力されていた`slow`を含んだものを条件にしています。ただし、`insertId`が具体的にどういった仕様で決定されているかは未確認です。
:::
::::

また、特定のユーザーグループへのメンションを含む本文を作成します。
```
slow queryが検知されました
<!subteam^S999ABCDEFG>
```
本文中でSlackのユーザーグループにメンションするには、少し特殊な書き方が必要なようです。（[Mentioning groups](https://cloud.google.com/logging/docs/logs-based-metrics/charts-and-alerts?hl=ja)を参考）

上記までをTerraformで書くと以下のようになります。

:::details Terraform
```Terraform
# ログベースのアラートポリシーの作成（スロークエリ）
resource "google_monitoring_alert_policy" "slow_query_alert_policy" {
  display_name          = "スロークエリ監視"
  notification_channels = [google_monitoring_notification_channel.slack_notify.name]
  combiner              = "OR"

  conditions {
    display_name = "slow_query_logs"
    condition_matched_log {
      filter = "resource.type=\"cloudsql_database\" insertId=~\".*slow.*\""
    }
  }

  alert_strategy {
    notification_rate_limit {
      period = "300s"
    }
    auto_close = "604800s"
  }

  documentation {
    content = "スロークエリが発生しました。  \n<!subteam^S999ABCDEFG>"
  }
}
```
:::

ここまでで必要なリソースが準備できたので、スロークエリが発生すると下記のような通知が飛ぶようになります。
![](https://storage.googleapis.com/zenn-user-upload/55b076ccde46-20240301.png)


終わり。

## 参考
https://cloud.google.com/sql/docs/mysql/diagnose-issues?hl=ja#query-logs
https://dev.mysql.com/doc/refman/8.0/en/slow-query-log.html
https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_long_query_time
https://cloud.google.com/logging/docs/logs-based-metrics/charts-and-alerts?hl=ja
https://api.slack.com/reference/surfaces/formatting
https://cloud.google.com/monitoring/support/notification-options?hl=ja#slack
