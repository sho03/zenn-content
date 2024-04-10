---
title: "publishされたファイルをCloud Storageに保存する"
emoji: "💬"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["gcp"]
published: true 
publication_name: "welmo"
---

## やりたいこと
Cloud pub/subでpublishされたメッセージデータをCloud Storageに保存します。
また、以下の条件のもと実現する方法を考えます。

* publishされるメッセージはJSON形式
* JSONデータを元にCloud Storageに保存するファイル名を生成する
* pub/subトピックが外部組織（別プロジェクト）に存在している

調べた限りだと以下のような方法が考えられるようです。

* Cloud Functionsの[pub/sub](https://cloud.google.com/functions/docs/calling/pubsub?hl=ja)トリガーを使用して、トピックにパブリッシュされたタイミングでCloud Functionsを実行する
* Cloud Functionsの[HTTPトリガー](https://cloud.google.com/functions/docs/calling/http?hl=ja)を使用して、[プッシュサブスクリプション](https://cloud.google.com/pubsub/docs/push?hl=ja)でCloud FunctionsへHTTPリクエストする
* [Cloud Storage サブスクリプション](https://cloud.google.com/pubsub/docs/cloudstorage?hl=ja)を使用する

このうち、前述した要件を満たしそうなのがCloud FunctionsのHTTPトリガーだったため、HTTPトリガーを使ってCloud Storageへファイルを保存することにします。

::::details 他を断念した理由
:::message
* Cloud Functionsのpub/subトリガーでは別プロジェクトに存在するトピックをトリガーにすることができないようなので断念しました。
* Cloud Storageサブスクリプションは、ファイル名を細かく設定できず、ファイル名は`<UTC-date-time>_<uuid>`となるようです。ただし、接頭辞や接尾辞は設定できるようなので、これでよければ方法としては一番簡単そうに思います（[ファイル名の接頭辞と接尾辞](https://cloud.google.com/pubsub/docs/create-cloudstorage-subscription?hl=ja#file_names)を参考）。
:::
::::

というわけで、Cloud pub/subとCloud FunctionsのHTTPトリガーを利用してCloud Storageへデータを保存する方法を説明していきます。

## やっていく

一つずつ必要なリソースを作成していきます。
1. Cloud Storage
2. Cloud Functions
3. Cloud pub/sub

の順で行います。

### Cloud Storageを作成する
Cloud Storageは、以下のコマンドを実行します。
```
$ gcloud storage buckets create gs://test-http-trigger-bucket --location=asia-northeast1
```

### Cloud Functionsを作成する
次に、[HTTPトリガー](https://cloud.google.com/functions/docs/calling/http?hl=ja)で実行するCloud Functionsを作成します。
後に作成するサブスクリプションで設定されたURLを利用します。
まず、以下のファイルを作成します。

今回はJavaScriptで関数を作成しますが、CloudFunctionsの実行環境はいくつか用意されています。[`gcloud functions runtimes list`](https://cloud.google.com/sdk/gcloud/reference/functions/runtimes/list)コマンドなどを使用してみてください。

```js:index.js
const functions = require('@google-cloud/functions-framework');
const dayjs = require('dayjs');
const { Storage } = require("@google-cloud/storage");

functions.http('myHttpFunction', (req, res) => {
  const base64data = req.body.message.data;

  if (!base64data) {
    console.log("failed recieving data.");
    res.send('error')
  }

  const currentTime = dayjs().format("YYYY-MM-DDTHH:mm:ss");

  const jsonString = Buffer.from(base64data, 'base64');
  const json = JSON.parse(jsonString);
  const fileName = `${currentTime}-${json.title}.json`
  console.log(fileName);

  const storage = new Storage();
  // 作成したCloud Storageの名前を指定する
  const myBucket = storage.bucket(<storage-name>);
  const file = myBucket.file(fileName);
  const contents = jsonString;
  file.save(contents).then(function() {});

  res.send('OK');
});

```

```json:package.json
{
  "dependencies": {
    "@google-cloud/functions-framework": "^3.0.0",
    "@google-cloud/storage": "7.7.0",
    "dayjs": "1.11.10"
  }
}

```

作成したファイルをデプロイします。
```
$ gcloud functions deploy http-trigger \
--entry-point=myHttpFunction \
--region=asia-northeast1 \
--runtime=nodejs20 \
--trigger-http \
--gen2
```

作成されたことを確認します。
```
$ gcloud functions list
NAME          STATE   TRIGGER       REGION           ENVIRONMENT
http-trigger  ACTIVE  HTTP Trigger  asia-northeast1  2nd gen
```

### サブスクリプションを作成する
[gcloud pubsub subscriptions create](https://cloud.google.com/sdk/gcloud/reference/pubsub/subscriptions/create)コマンドを使用して任意のトピックに紐づくサブスクリプションを作成します。この時、サービスアカウントに[デフォルトコンピュートアカウント](https://cloud.google.com/compute/docs/access/service-accounts?hl=ja#default_service_account)を指定することで、認証されるようにします。
また、`push-endpoint`には先に作成したCloud Functionsのエンドポイントを指定します。
```
$ gcloud pubsub subscriptions create my-subscription \
--topic=my-topic \
--topic-project=cloud-test-415104 \
--push-endpoint=https://asia-northeast1-cloud-test-415104.cloudfunctions.net/http-trigger \
--push-auth-service-account=9999999999-compute@developer.gserviceaccount.com
```

### パブリッシュしてCloud Storageに保存されることを確認する
上記までで必要なリソースの設定が完了したので、メッセージをパブリッシュしてCloud Storageへ保存されるか試してみます。
```
$ gcloud pubsub topics publish my-topic --message='{"title": "helloworld"}'
```
Storageの中にファイルが作成されているかを確認します。
```
$ gcloud storage ls --recursive "gs://test-http-trigger-bucket/**"
gs://test-http-trigger-bucket/2024-02-27T01:42:06-helloworld.json
```
```json:中身
{"title":"helloworld"}
```
大丈夫そうです。

## 参考
https://cloud.google.com/pubsub/docs/push?hl=ja#receive_push
https://cloud.google.com/functions/docs/concepts/version-comparison?hl=ja
