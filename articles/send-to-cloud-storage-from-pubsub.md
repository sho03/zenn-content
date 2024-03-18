---
title: "pub/subトリガーを使用してメッセージをCloud Storageに保存する"
emoji: "🙆"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['gcp', 'javascript']
published: false
publication_name: "welmo"
---

## この記事は
特定のtopicにpublishされたメッセージを、Cloud Functionsを介してCloud Storageに保存する方法を解説します。

関連する公式ドキュメントは、[pub/subトリガー](https://cloud.google.com/functions/docs/calling/pubsub?hl=ja)が参考になります。

なお、本稿では各リソースの作成に[`gcloud`](https://cloud.google.com/sdk/gcloud?hl=ja)コマンドを使用します。
コンソールがやりやすい方は適宜公式ドキュメントなどを参照してください。

## やっていく
それでは一つずつ必要なリソースを作成していきます。
1. Cloud pub/sub
2. Cloud Storage
3. Cloud Functions
の順で行います。（いずれも作成する際に前の手順で作成したリソースの情報を一部使用します。）

### Cloud Pub/Subの設定
まず初めに、Cloud FunctionのトリガーとなるPub/Subを設定します。

```
$ gcloud pubsub topics create <topic-name>
```
想定としてはこのトピックに以下のようなJSONがパブリッシュされ、パブリッシュされた時刻情報と`title`キーからファイル名を生成します。

```json
{
  "title": "hello"
}
```
上記の例では、`20240226T18:50:25-hello.json`のようなファイルがCloud Storageに保存されます。

### Cloud Storageの設定
続いてCloud Storageにファイルを保存するためのバケットを作成します。
```
$ gcloud storage buckets create gs://<bucket-name> --location=<bucket-location>
```

### Cloud Functionsの設定
最後に、publishしたデータをCloud Storageへ保存するためのCloud Functionsを用意します。
まず初めに、適当なディレクトリに以下の2つのファイルを作成します。
```js:index.js
const functions = require('@google-cloud/functions-framework');
const {Storage} = require('@google-cloud/storage');
const dayjs = require('dayjs');

functions.cloudEvent('sendJson', cloudEvent => {
  // The Pub/Sub message is passed as the CloudEvent's data payload.
  const base64data = cloudEvent.data.message.data;

  if (!base64data) {
    console.log("failed recieving data.");
    return;
  }

  const currentTime = dayjs().format("YYYY-MM-DDTHH:mm:ss");

  const jsonString = Buffer.from(base64data, 'base64');
  const json = JSON.parse(jsonString);
  const fileName = `${currentTime}-${json.title}.json`
  console.log(fileName);

  const storage = new Storage();
  // bucketの名前を指定する
  const myBucket = storage.bucket('<bucket-name>');
  const file = myBucket.file(fileName);
  const contents = jsonString;
  file.save(contents).then(function() {});
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
続いて、作成したファイルをCloud Functionsへデプロイします。
以下のコマンドを、上記のファイルがあるディレクトリで実行します。
```
$ gcloud functions deploy nodejs-pubsub-function \
--gen2 \
--runtime=nodejs20 \
--region=asia-northeast1 \
--source=. \
--entry-point=sendJson \
--trigger-topic=<topic-name>
```

デプロイが完了後、以下のようになっていればOKです。
```
$ gcloud functions list
NAME                    STATE   TRIGGER            REGION           ENVIRONMENT
nodejs-pubsub-function  ACTIVE  topic: my-topic    asia-northeast1  2nd gen
```

### publishしてみる
上記までで一通りの設定が完了したので、実際にJSONテキストをpublishして、Cloud Storageに保存されるか試してみます。

```
$ gcloud pubsub topics publish my-topic --message='{"title": "hello"}'
messageIds:
- '10519970147216947'

```
念の為ファイルがバケット内に作成されているか確認します。
```
$ gcloud storage ls --recursive "gs://<bucket-name>/**"
gs://test-pubsub-trigger-bucket/2024-02-26T06:58:20-hello.json
gs://test-pubsub-trigger-bucket/2024-02-26T06:59:07-hello.json
```

## 参考
https://cloud.google.com/functions/docs/calling/pubsub?hl=ja
https://qiita.com/NOGU626/items/1af991b1757988b6cee6
https://cloud.google.com/nodejs/docs/reference/storage/latest
https://cloud.google.com/pubsub/docs/publisher?hl=ja#publish-messages
https://cloud.google.com/storage/docs/listing-objects?hl=ja#command-line
