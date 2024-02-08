---
title: "Terraform 入門"
emoji: "📚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['terraform', 'gcp']
published: false
---

# Terraformとは

# よく使うコマンド

## terraform init

## terraform validate

## terraform fmt

## terraform plan

## terraform apply

## terraform destroy

# 実際にGoogle Cloudにリソースを作ってみる

# モジュール

# tfstate

# ファイルを分割する

# 変数定義ファイルを作成する

# localsで変数を定義する
https://developer.hashicorp.com/terraform/language/values/locals

```tf
locals {
  service_name = "forum"
  owner        = "Community Team"
}
```

```tf
resource "aws_instance" "example" {
  # ...

  tags = local.common_tags
}
```