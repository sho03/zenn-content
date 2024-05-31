---
title: "Terraform applyは途中で失敗してもリソースを作成している"
emoji: "📌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['terraform']
published: false
---
## この記事は
`terraform apply`をGithubActionsで実行しているのですが、途中でリソースの作成に失敗しても失敗より前に実行されていたリソース作成は有効になっているというお話

## 事象
1. `terraform apply`でCloud Functions/Cloud pubsubのリソースを作成しようとする
2. `terraform apply`が失敗し、原因がArtifactRegistryの有効化ができていなかったため、GCPのコンソールより有効化する
3. `terraform plan`が失敗する。
```
Error: Invalid index

  on pubsub.tf line 263, in resource "google_pubsub_subscription" "send_to_storage_for_estimated_behavior":
 263:     push_endpoint = google_cloudfunctions2_function.estimated_behavior.service_config[0].uri
    |----------------
    | google_cloudfunctions2_function.estimated_behavior.service_config is empty list of object

The given key does not identify an element in this collection value.
```

## 原因
Cloud Functionsが名前だけ作成され、中身がなかったために`uri`が存在しない状態となっていた。（？）

## 雑記
てっきり`terraform apply`はデータベースのACID特性でいうAtomicity（原子性）と同様に全て実行されるか全く実行されないかの2択だと誤解してました。

## 参考
