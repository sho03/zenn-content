---
title: "Terraformの基本"
emoji: "🙆"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['terraform']
published: false
publication_name: "welmo"
---

## この記事は

## ファイルを分割する

## tfenvを使う
### tfenv installで使用したいTerraformバージョンをインストールする
### tfenv listでインストール済みのバージョンをリストする
### pinで.terraform-versionファイルを生成する

## 環境ごとの値を使う
https://developer.hashicorp.com/terraform/language/values/variables
- variables.tf
- xxxx.tf

## ローカル変数を使う
https://developer.hashicorp.com/terraform/language/values/locals

## tfstateをクラウド環境に保存する
https://developer.hashicorp.com/terraform/language/settings/backends/configuration
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
