---
title: "Terraformを学ぶ"
emoji: "🙆"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['terraform', 'gcp']
published: false
publication_name: "welmo"
---

## この記事は
なお、GCPが公式で出している[Terraformを使用するためのベストプラクティス](https://cloud.google.com/docs/terraform/best-practices-for-terraform?hl=ja)を大いに参考にしているので、こちらも併せて読んでいただければと思います。

## ファイルを分割する
`*.tf`というファイルを作成すると、それらがモジュールとして`terarform`コマンドで自動的に検出されるようになります。
そのため、基本的には作成したいリソースごとにtfファイルを作成するとどのようなリソースが作成されるかの見通しがよくなります。
```shell:イメージ
provider.tf
cloud_run.tf
cloud_sql.tf
```

## 環境ごとの値を使う
https://developer.hashicorp.com/terraform/language/values/variables
- variables.tf
- xxxx.tf

## ローカル変数を使う
https://developer.hashicorp.com/terraform/language/values/locals

## tfstateをクラウド環境に保存する
https://developer.hashicorp.com/terraform/language/settings/backends/configuration
https://cloud.google.com/docs/terraform/resource-management/store-state?hl=ja
https://qiita.com/ymmy02/items/e7368abd8e3dafbc5c52

```tf:main.tf
terraform {
  required_version = "= 1.7.4"
  required_providers {
    google = {
      version = "= 5.19.0"
      source  = "hashicorp/google"
    }
    google-beta = {
      version = "= 5.19.0"
      source  = "hashicorp/google-beta"
    }
  }
  # これがterraform stateを保存するバケットになる
  backend "gcs" {
    bucket = "terraform-state"
  }
}
```
なお、`terraform init`時点でバケットが存在している必要があるため、以下のいずれかの方法を取る必要があります。
1. `terraform init`時点では`backend`を指定せずに、バケットのみを定義する。
2. `terraform init`の前に手動でコンソールからバケットを作成する。

## .terraformとterraform.lock.hcl

## dynamic

## for_each

## depends_on

## tfenvを使う
### tfenv installで使用したいTerraformバージョンをインストールする
### tfenv listでインストール済みのバージョンをリストする
### pinで.terraform-versionファイルを生成する

## 条件付きでリソースを作成する
https://cloud.google.com/docs/terraform/best-practices-for-terraform?hl=ja#count

## 作成済みのリソースをtfファイルとして出力する
https://cloud.google.com/docs/terraform/resource-management/export?hl=ja

## output
引用）https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/cloudfunctions2_function#example-usage---cloudfunctions2-basic
```tf
# like this
output "function_uri" { 
  value = google_cloudfunctions2_function.function.service_config[0].uri
}
```

## 参考
https://cloud.google.com/docs/terraform/get-started-with-terraform?hl=ja
https://cloud.google.com/docs/terraform/basic-commands?hl=ja
