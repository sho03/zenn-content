---
title: "Terraformをupgradeする"
emoji: "🐕"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform"]
published: false
---
## この記事は
`Terraform`を`v0.13.5`からアップグレードした時の記録

[Terraform provider for Google Cloud 5.0.0 Upgrade Guide](https://registry.terraform.io/providers/hashicorp/google/latest/docs/guides/version_5_upgrade)

## 最初の状態
古いですね。記事執筆時点（2024/04/11）ではTerraformは1.8、google providerは5.24.0です。
```tf:main.tf
terraform {
  required_version = "= 0.13.5"
  required_providers {
    google = {
      version = "= 4.7.0"
      source  = "hashicorp/google"
    }
    google-beta = {
      version = "= 4.7.0"
      source  = "hashicorp/google-beta"
    }
  }
}
```
[Best Practices for Provider Versions](https://developer.hashicorp.com/terraform/language/providers/requirements#best-practices-for-provider-versions)


## 参考
https://developer.hashicorp.com/terraform/language/v1.1.x/upgrade-guides
https://developer.hashicorp.com/terraform/language/v1.1.x/upgrade-guides/0-14
https://zenn.dev/smartround_dev/articles/7368bcd8965064?redirected=1
