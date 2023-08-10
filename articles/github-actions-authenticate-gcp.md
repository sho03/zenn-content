---
title: "Github ActionsからGCPのリソースを使用する（認証）"
emoji: "😺"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["githubactions"]
published: false
---

# この記事は
Github Actionsを用いてGCPへのデプロイをするなど、GCP上のリソースにアクセスする際の方法を書きます。
最終的に、GCPのCloud storage上にあるファイルの一覧を取得することを目標とします。

アクセス方法を大別すると以下の2つの方法があるようですが、[ドキュメント](https://github.com/google-github-actions/setup-gcloud#authorization)でも**preferred**となっている`Workload Identity Federation`を扱います。
- `Workload Identity Federation`
- `Service Account Key JSON authentication`


# Workload Identity FederationとService Account Key JSON authentication
この記事ではWorkload Identity Federationを扱う、と書きましたが、簡単にService Account Key JSON authenticationとの違いも示しておきます。
[こちら](https://github.com/google-github-actions/setup-gcloud#authorization)にあるように、Workload Identity Federationを利用することでクレデンシャルファイルの保存が不要になります。下記がそれぞれの方法のおおまかな手順です。

| 手順 | Workload Identity Federation | Service Account Key JSON authentication |
|---|---|---|
| 1  |> |hoge|
| 2  |   |   |
| 3  |   |   |



# 方法

## 前提条件
手順を実施する前に、以下の準備が必要となります。
* xxx
* xxx
* xxx
* xxx


方法は3つのステップに分かれます。
1. GCP側の設定([Workload Identity 連携の構成](https://cloud.google.com/iam/docs/configuring-workload-identity-federation?hl=ja#github-actions))
    1. [Workload Identityプール](https://cloud.google.com/iam/docs/workload-identity-federation?hl=ja#pools)の作成
    1. サービスアカウントの作成
    1. サービスアカウントの作成
1. Githun Actionsワークフローの作成

順に見ていきましょう。

## 1. サービスアカウントの作成

## 2. Github Actionsワークフローの作成

# （おまけ）この記事を書いたきっかけ
Github Actionsの実行結果に、
```
"service_account_key" has been deprecated. Please switch to using google-github-actions/auth which supports both Workload Identity Federation and Service Account Key JSON authentication. For more details, see https://github.com/google-github-actions/setup-gcloud#authorization
```

が出ていました。ワーニングによると、`google-github-actions/auth`は`Workload Identity Federation`と`Service Account Key JSON authentication`をサポートしているようなので、そちらに切り替えます。

このワーニングに限らずGithub Actions上で利用されているものでdeprecatedになったものはワーニングが出るので、なるべく早めに対応するようにしましょう。

# 参考
https://docs.github.com/ja/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-google-cloud-platform
https://cloud.google.com/blog/ja/products/identity-security/enabling-keyless-authentication-from-github-actions
https://github.com/terraform-google-modules/terraform-google-github-actions-runners/tree/master/modules/gh-oidc