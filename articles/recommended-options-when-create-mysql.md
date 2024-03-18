---
title: "TerraformでMySQLを作成する"
emoji: "🙆"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['terraform', 'mysql']
published: false
publication_name: "welmo"
---

## この記事は

## 最低限作成しないといけないリソース
- Cloud SQL インスタンス
- Cloud SQL データベース
- Cloud SQL ユーザー

他に、IP制限など諸々ありますが、一旦は上記のリソースさえ作成すればアプリからDB利用をするのに十分な状態になると思います。

## Terraform file
はじめにTerraformのファイルを載せます。
一つずつ見ていきましょう。

:::details サンプル
```tf:sql.tf
resource "google_sql_database_instance" "main" {
  project          = var.project
  name             = "main-mysql"
  database_version = "MYSQL_8_0"
  region           = "asia-northeast1"

  settings {
    # Second-generation instance tiers are based on the machine
    # type. See argument reference below.
    tier = "db-f1-micro"
  }
}
```
:::

## google_sql_user
https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/sql_user
