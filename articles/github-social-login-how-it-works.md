---
title: "「Githubで認証する」は何をしているのか"
emoji: "😎"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["OAuth", ]
published: false
---

# この記事は
よくWebアプリ等で見かける「Githubでログインする」というのは実のところどのような仕組みで動いているのかを調べた結果を書きます。
また、実装についてもつまづいた部分などを書き記しています。

最終的に、簡単なWebアプリを作成して「Githubでログインする」ボタンでログインできることを目標にします。

なお、本記事では`Auth0`や`Firebase Auth`などのIDaasは扱いません。


:::message
本記事ではソーシャルログインの解説に主軸を置いているため、Githubによるログインのみを扱います。
現実的にはLINEやFacebookなどでログインできるように拡張することがあると思いますので、はじめから`Auth0`や`Firebase Auth`などのいわゆるIDaasを使用することの検討をお勧めします。
:::

# ソーシャルログインの仕組み
以下に、ソーシャルログインの仕組みを掲載します。


# 実装

# 参考
https://docs.github.com/en/developers/apps/building-oauth-apps/authorizing-oauth-apps
https://stackoverflow.com/questions/71741596/how-do-i-implement-social-login-with-github-accounts
https://blog.4breaker.com/2020/06/14/post-1378/