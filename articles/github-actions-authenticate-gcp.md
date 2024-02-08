---
title: "Github ActionsからGCPのリソースを使用する"
emoji: "😺"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["githubactions"]
published: false
---

# この記事は
Github Actionsを用いてGCPへのデプロイをするなど、GCP上のリソースにアクセスする際の方法を書きます。
最終的に、GCPのCloud storage上にあるファイルの一覧を取得することを目標とします。

# 問題
Github Actionsの実行結果に、
```
"service_account_key" has been deprecated. Please switch to using google-github-actions/auth which supports both Workload Identity Federation and Service Account Key JSON authentication. For more details, see https://github.com/google-github-actions/setup-gcloud#authorization
```
が出ていました。ワーニングによると、`google-github-actions/auth`は`Workload Identity Federation`と`Service Account Key JSON authentication`をサポートしているようなので、そちらに切り替えます。

# やってみる
[ドキュメント](https://github.com/google-github-actions/setup-gcloud#authorization)で**preferred**となっている[`Direct Workload Identity Federation`](https://github.com/google-github-actions/auth?tab=readme-ov-file#preferred-direct-workload-identity-federation)を扱います。

```shell
# 
gcloud config configurations list

export PROJECT_ID=your-project-id

gcloud iam workload-identity-pools create "github" \
  --project="${PROJECT_ID}" \
  --location="global" \
  --display-name="GitHub Actions Pool"

gcloud iam workload-identity-pools describe "github" \
  --project="${PROJECT_ID}" \
  --location="global" \
  --format="value(name)"

# result
projects/593752064275/locations/global/workloadIdentityPools/github

gcloud iam workload-identity-pools providers create-oidc "monitoring-project" \
  --project="${PROJECT_ID}" \
  --location="global" \
  --workload-identity-pool="github" \
  --display-name="My GitHub repo Provider" \
  --attribute-mapping="google.subject=assertion.sub,attribute.actor=assertion.actor,attribute.repository=assertion.repository" \
  --issuer-uri="https://token.actions.githubusercontent.com"
Created workload identity pool provider [monitoring-project].

gcloud iam workload-identity-pools providers describe "monitoring-project" \
  --project="${PROJECT_ID}" \
  --location="global" \
  --workload-identity-pool="github" \
  --format="value(name)"
```


# 参考
https://docs.github.com/ja/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-google-cloud-platform
https://cloud.google.com/blog/ja/products/identity-security/enabling-keyless-authentication-from-github-actions
https://github.com/terraform-google-modules/terraform-google-github-actions-runners/tree/master/modules/gh-oidc
https://cloud.google.com/iam/docs/workload-identity-federation?hl=ja#pools
https://cloud.google.com/iam/docs/configuring-workload-identity-federation?hl=ja#github-actions
https://christina04.hatenablog.com/entry/workload-identity-federation
https://zenn.dev/satohjohn/articles/1645be8e83eab6
https://qiita.com/mintak21/items/5232a089f3a39a71b155