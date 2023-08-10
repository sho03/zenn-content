---
title: "Cloud RunからCloud Storageにアクセスする"
emoji: "🔥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["gcp", "springboot"]
published: false
---

# この記事は
Cloud Runで起動しているアプリケーション（本記事ではSpringBootを使用）から、Cloud Storageに保存されているオブジェクトにアクセスする方法を記載します。

# 解説
Cloud Runはデフォルトでは[Compute Engineサービスアカウントとして実行される](https://cloud.google.com/run/docs/configuring/service-accounts?hl=ja)。
そのため、Compute Engineサービスアカウントに対してストレージアクセス権限（`storage.buckets.get`）を付与する必要がある。

ただし、デフォルトのサービスアカウントを使用するよりも、[ユーザー管理サービスアカウントを割り当てる方法](https://cloud.google.com/run/docs/securing/service-identity?hl=ja)を推奨しているので、こちらの方法をとったほうが良い。
本記事ではデフォルトサービスアカウントでの実施方法を記載する。


# 参考
https://stackoverflow.com/questions/65135272/how-to-access-to-a-google-cloud-storage-bucket-from-a-cloud-run-service-without