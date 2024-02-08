---
title: "Cloud Runが静的IPアドレスでCloudSQLへ接続できるようにする"
emoji: "🤖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["cloudrun"]
published: false
---

# 問題
Cloud RunからCloud SQLへ接続したいが、Cloud SQL側ではIPアドレスを制限している。ただし、CloudRunではデフォルトで動的IPアドレスが割り振られるため、なんらかの方法でCloudRunが外部のリソースへ接続する際に静的IPアドレスを利用するようにしたい。

#　方法
CloudRunにVPCアクセスコネクタを使用してVPC経由でCloudRunへ接続するようにする。また、VPCはCloudNATを利用することで静的IPアドレスが利用できるようになる。

before 
```mermaid

```

after
```mermaid
```

# terraformファイル
上記までで実践した内容のterraformファイルを下記に示す。

[Google Provider](https://registry.terraform.io/providers/hashicorp/google/latest/docs/guides/provider_reference)
[Terraform Settings](https://developer.hashicorp.com/terraform/language/settings)
```tf
```

#　参考資料
https://cloud.google.com/run/docs/configuring/static-outbound-ip?hl=ja
https://blog.g-gen.co.jp/entry/cloudrun-using-static-ip
https://chimbu.medium.com/static-ip-for-google-cloud-cloud-functions-internet-access-serverless-vpc-access-cloud-nat-18cb4b89fb8e
