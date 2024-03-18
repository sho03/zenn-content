---
title: "Backlogの変更をSlackでメンションする"
emoji: "🔖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["backlog", "slack", "nodejs"]
published: false
---

## この記事でやること
`Backlog`で何らか自分に関連する更新があった場合にSlackに通知します。具体的には以下の場合を想定します。
* タスクの新規作成時、アサインされた人にSlack通知する
* タスクの更新時、担当者にSlack通知する
* タスクのコメントの「コメントをお知らせしたいユーザー」へSlack通知する

Backlogで何らか更新すると、以下のような通知が届きます。
![](https://storage.googleapis.com/zenn-user-upload/4a4af9d1affb-20240214.png)

## 実行環境など

* BacklogのWebhookに登録するWebサーバにはNode.jsを使用します。
* インフラにはGoogleCloudを利用します。
* BacklogとSlackに登録されているユーザーのメールアドレスが同じであることを前提としています。（下記参照）

:::message
BacklogとSlackはユーザー管理体系が異なるため、両者に共通するメールアドレスを利用することで、タスクに関連する更新がSlack上では誰に当たるのかを特定しています。
:::

## 処理概観

```mermaid
// TODO:
```

## 実装していく
BacklogのWebhookを定義する際に、実行するアプリのURLが必要になるため、先に単純なWebアプリをデプロイしその後にWebhookの設定をしていく手順で実施します。

### 基本のサーバをデプロイする
デプロイ先はどこでもいいのですが、個人的に使い慣れている`Google Cloud`へ今回はデプロイします。
```javascript
const express = require('express');

const app = express();
const port = 8080;

app.use(express.json());

app.post('/postjson', (req, res) => {
  const data = req.body;
  console.log(data); 
  res.json(data);
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```

### Webhookを登録する
一度Backlogに戻り、[Webhook](https://support-ja.backlog.com/hc/ja/articles/360036147713-Webhook)の登録をします。この時点ではアプリがリクエストを受信しただけでSlackへの通知は行っていません。
Webhookの設定が完了すると、テストができるようになるのでまずはそこを目指します。

一例として、以下のような設定になります。
![](https://storage.googleapis.com/zenn-user-upload/706c26ce1a60-20240214.png)

### typeごとに処理を実装する
[公式ドキュメントの「最近の更新の取得」](https://developer.nulab.com/ja/docs/backlog/api/2/get-recent-updates/#)より、`type`を参照し、処理を記述していきます。
前述したように、今回はタスクの追加・更新・コメントのみを処理対象とするので、以下の`type`が対象になります。
なお、非公式ですが、Webhook実行時にBacklogから送信されるJSONは[Backlogから送信されるPOSTリクエストの内容](https://qiita.com/rekooom/items/455c1e8ec247e3cb8abb)が参考になります。

| 数値 | 種別|
| ---- | ---- |
| 1 | タスクの追加|
| 2 | タスクの更新 |
| 3 | タスクへのコメント|


### Backlogのユーザー情報を取得する
Backlog APIのgetUserを利用して、ユーザー情報を取得します。このあと、取得したユーザー情報のメールアドレスを利用することでSlackユーザーを特定します。
初めてBacklogのAPIを使用する方は[APIの設定](https://support-ja.backlog.com/hc/ja/articles/360035641754-API%E3%81%AE%E8%A8%AD%E5%AE%9A)を参考にAPIキーを取得してください。
```javascript
  const token = "<your-api-key>"
  const url = `https://<your-space>.backlog.com/api/v2/users/${userId}?apiKey=${token}`
  const response = await fetch(url);
  const json = await response.json();
  // Slack APIで利用するメールアドレス
  const { mailAddress } = json;
```

### Slack APIを用いて通知する
前述したBacklogのユーザー情報（メールアドレス）を使って、Slackの情報を取得します。
SlackのAPIを初めて使う方は[Slack APIを使用してメッセージを投稿する](https://zenn.dev/kou_pg_0131/articles/slack-api-post-message)などを参考に設定ください。
なお、後述するメールアドレス取得APIには`users:read.email`の権限が必要になります。チャット投稿用の`chat:write`と併せて設定ください。

```javascript
  const slackToken = "<your-token>";
  const client = new WebClient(slackToken);
  // Backlogのユーザー情報から取得したメールアドレスを使用してSlackのユーザー情報を得る
  const slackUserResponse = await client.users.lookupByEmail({ email: mailAddress });
  const { user }= slackUserResponse;

  const channel = '#my-channnel';
  // Slack上でユーザーにメンションをさせるには<@UXXXXXXX>の書き方をします https://api.slack.com/reference/surfaces/formatting#mentioning-users
  const message = `<@${user.id}> message`
  const client = new WebClient(slackToken);
  const response = await client.chat.postMessage({ channel, text: message });
```

## 参考
https://developer.nulab.com/ja/docs/backlog/api/2/get-recent-updates/#
https://developer.nulab.com/ja/docs/backlog/#
https://zenn.dev/kou_pg_0131/articles/slack-api-post-message
https://api.slack.com/reference/surfaces/formatting#mentioning-users
https://developer.nulab.com/ja/docs/backlog/api/2/get-user/#
https://api.slack.com/methods/users.lookupByEmail
https://qiita.com/rekooom/items/455c1e8ec247e3cb8abb
