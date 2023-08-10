---
title: "GCPで支払いプロファイルを作成するとき"
emoji: "📘"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["gcp"]
published: false
---

# 問題
GCPの支払いプロファイルを退職済みユーザーのアカウントで作成していたため、アカウント削除に伴い支払いプロファイルも消え、GCPの支払いができなくなった。

# 対策
* GCP（Google）の支払いプロファイルには「個人」と「ビジネス」が存在するが、必ず「ビジネス」を選択する
* ビジネスとして設定したお支払いプロファイルに必ず[別のユーザーを追加する](https://support.google.com/paymentscenter/answer/7162853?hl=ja)（[バス係数](https://note.com/gengo_k/n/n32f2e881f1cc)も参考）

# 支払いプロファイルの種類
[お支払いプロファイルのアカウントの種類について](https://cloud.google.com/billing/docs/how-to/modify-contacts?hl=ja#account-types)にあるように、一度作成したアカウントの種類の設定の変更はできません。作成する際には注意しましょう。
また、


# 調べたこと
* 共有ユーザーを作れば良いのでは？
[ユーザー間でアカウントを共有しない](https://support.google.com/a/answer/33330?hl=ja)や、[管理者アカウントをユーザー間で共有しない](https://support.google.com/a/answer/9011373#dont-share)によれば、共有アカウントを作成することは推奨されていないため、この方法は取れないようです。

# （おまけ）新しい請求アカウントの追加及び支払いプロファイルの登録
GCPでは、請求アカウントに新しい支払いプロファイルを追加することができないため、請求アカウント自体を新しく作成し直す必要があります。
