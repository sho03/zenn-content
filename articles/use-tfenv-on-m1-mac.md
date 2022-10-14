---
title: "tfenvを使ってTerraformの使用バージョンを管理する"
emoji: "🐕"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform"]
published: false
---

# この記事で書くこと
`tfenv`を使用してTerraformの異なるバージョンを使用できるようにします。
Terraformでは`.tf`ファイル内で`required_version = "= X.X.X"`によりTerraformのバージョンを指定できますが、異なるバージョンを使用したい場合にバージョンを切り替えるのが面倒になることがあります。

そこで、本記事では`tfenv`を使用して各ディレクトリごとにTerrformのバージョンを指定できるようにしていきます。

# 環境

# 手順

## インストール
```shell
$ brew install tfenv
```
## 使用するバージョンのインストール
```shell
$ tfenv install 1.0.1

# 確認
$ tfenv list
  1.2.9
* 0.13.5 (set by /opt/homebrew/Cellar/tfenv/3.0.0/version)
```

ただし、M1 Macかつ、terraform v1.0.1以前をインストールする場合は以下のコマンドを実行します。([こちら](https://zenn.dev/shonansurvivors/articles/876425b90d2dee)を参考）
```shell
$ TFENV_ARCH=amd64 tfenv install 1.0.1
```


## 使用するバージョンの指定
以下のコマンドを実行することで使用するTerraformのバージョンを指定できます。
```shell
$ tfenv use 1.0.1

# 現在のバージョンの確認
$ terraform -v
v1.0.1
```

### ディレクトリごとにバージョンを指定する

