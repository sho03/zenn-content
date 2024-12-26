---
title: "GCPのDocumentation AIを使う"
emoji: "👏"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['gcp', 'ai']
published: false
---
## この記事は
Documentation AIを使ってみた時の記録です。

## どのプロセッサを使うか
https://cloud.google.com/document-ai/docs/overview#which-processor
この表にある通り、独自のフォーマットを読み取りたい場合は、`Custom Processor`を使用すると良いそうです。

## Document AIを使用する
https://cloud.google.com/document-ai/docs/overview#using-processors
1. プロセッサの選択
1. プロセッサの作成
1. プロセッサの訓練
1. ドキュメントの解析

## Custom Document Extractorを使う
https://cloud.google.com/document-ai/docs/workbench/build-custom-processor

## 学習用のデータセットをexport / 共有する

## プロセッサを開発と本番で分ける

## ラベルのベストプラクティス
https://cloud.google.com/document-ai/docs/workbench/label-documents#name-fields
1. 名前とフィールドの名前を同じにする
1. スペースの代わりに`_`を使う
1. いろいろな名前を試す

## 親子要素
要素は親子要素を作成できる。
ただし、ネストすることはできない模様。
![](https://storage.googleapis.com/zenn-user-upload/959d1c12099e-20240328.png)

## テーブル構造をラベリングする時
https://cloud.google.com/document-ai/docs/workbench/label-documents#quick_tables

## Document AIのレスポンスを取得したい
https://cloud.google.com/document-ai/docs/handle-response

## 方法
[Custom Classifier](https://cloud.google.com/document-ai/docs/processors-list#processor_CUSTOM_CLASSIFICATION_PROCESSOR)でファイル種別を特定できる？さらに、ファイル種別に従って[Custom Extractor](https://cloud.google.com/document-ai/docs/processors-list#processor_cde)で情報を読み取る様にすれば良さそうかもしれない。

1. 複数のファイルが含まれている（数ページに渡る）ファイルを読みこむ
1. ページごとにファイルの種類を識別（Custom Classifier）
1. ファイルの種別ごとにCustom Extractorを用いて情報を抽出する
・・・みたいな感じか？

## 参考

https://cloud.google.com/document-ai/docs/workbench/cde-with-genai#build_and_evaluate_a_generative_ai_model
