---
title: "ブラウザからバーコードリーダーを起動する"
emoji: "🤖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["js"]
published: false
---

## この記事は
ブラウザで起動したWebアプリからバーコードを読み取って色々したい場合のあれこれを調査しました。
最終的な成果物はこちらのリポジトリにあります。

## ライブラリの調査
https://github.com/ericblade/quagga2
https://github.com/serratus/quaggaJS

## 実装について
### JANコードからデータを取得する
バーコードを読み取ることに成功した場合、読み取ったバーコード情報を利用して該当の製品情報を取得します。
https://www.jancode.xyz/
