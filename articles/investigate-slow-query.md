---
title: "スロークエリに対処する"
emoji: "🔥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['mysql', 'gcp']
published: false
---

## この記事は
スロークエリを検出したので調査と対処方法などを記録しました。
なお、最終的に実施した解決方法は以下のようになりました。

- なんか書く
- なんか書く

## 調査にあたって
データベースでクエリの問題が発生した時は**MENTORの原則**に従って調査していきます。
以降、この順に実際に行った調査をメモしていきます。
- Measure（計測）
- Explain（解析）
- Nominate（指名）
- Test（試験）
- Optimize（最適化）
- Rebuild（再構築）

参考書籍）[失敗から学ぶRDBの正しい歩き方](https://gihyo.jp/book/2019/978-4-297-10408-5)

## 計測する
詳細は[Cloud SQLでスロークエリを検知する](https://zenn.dev/welmo/articles/slow-query-log-on-cloud-sql)を参照ください。

```
# Time: 2024-03-04T10:09:55.207948Z
# User@Host: monitoring[monitoring] @  [cloudsqlproxy~35.203.250.4]  thread_id: 2045061  server_id: 2517013104
# Query_time: 32.868967  Lock_time: 0.000001 Rows_sent: 0  Rows_examined: 1049820
use monitoring_main;
SET timestamp=1709546962;
DELETE FROM home_appliance_usage WHERE home_appliance_usage.care_recipient_id = x'295B0D88CFFF48EDB1D5238666707D2D';
```

## 解析する
問題になっているクエリの特定ができたところで、どのような問題が発生しているかを解析します。
[`explain`](https://dev.mysql.com/doc/refman/8.0/ja/explain.html)を利用し、クエリの実行計画を取得します。

```
explain DELETE FROM home_appliance_usage WHERE home_appliance_usage.care_recipient_id = x'295B0D88CFFF48EDB1D5238666707D2D'\G
```

結果は以下のようになります。
出力結果は[EXPLAIN 出力フォーマット](https://dev.mysql.com/doc/refman/8.0/ja/explain-output.html)を参考に分析します。
```
           id: 1
  select_type: DELETE
        table: home_appliance_usage
   partitions: NULL
         type: range
possible_keys: PRIMARY,cr_id_idx
          key: PRIMARY
      key_len: 16
          ref: const
         rows: 1380158
     filtered: 100.00
        Extra: Using where
```

みたところ、`key`には`PRIMARY`が出力されているため、インデックスを用いた削除は実行されているように見えます。
となると、単純に削除対象となるレコード数が多いことによる影響と考えます。

## 指名する


## 試験する

## 最適化する

## 再構築する

## 参考
https://techblog.yahoo.co.jp/entry/2020052530001949/
https://medium.com/inet-lab/mysql%E3%81%AE%E5%B7%A8%E5%A4%A7%E3%81%AA%E3%83%86%E3%83%BC%E3%83%96%E3%83%AB%E3%81%AB%E3%81%8A%E3%81%91%E3%82%8Bdelete-b427e915d2de
https://qiita.com/daikiti1988/items/5311749b04a66547f22d
https://use-the-index-luke.com/ja/sql/dml/delete
https://zenn.dev/koduki/articles/d3e8984f420b370681f9
