---
title: "Dateとdayjs"
emoji: "🎉"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['javascript', 'dayjs']
published: false
---
## この記事は
`new Date()`で生成した現在時刻をリクエストに含めたところ、UTCになってしまっていたので色々調査しました。

## 事象
サーバサイドへのリクエストに現在時刻を含めるため、以下のようなコードを書いていました。
```js
const now = new Date().toISOString();
// 2024-06-28T07:28:50.183Z

const body = {
    startTime: now
};
post(body);
```

ただし、上記のようなコードだと時刻文字列がUTC（日本時間より9時間前）になってしまい、結果としてサーバサイドで処理する際に不正な値となっていました。


## 対処
`dayjs`を使用します。

:::message
JavaScriptで利用できる日付ライブラリは他にもあるので、`dayjs`以外でも同じような対処はできると思います。
参考（[dayjs vs moment.js](https://zenn.dev/akkie1030/articles/javascript-dayjs#dayjs-vs-moment.js)）
:::

`dayjs`はシステムのタイムゾーンを利用するようなので、現在の時刻を取得した際に日本時間の値が取得できます。

```js
const now = dayjs().format();
// 2024-06-28T16:28:50+09:00

const body = {
    startTime: now
};
post(body);
```


## 参考
https://zenn.dev/dmikurube/articles/curse-of-timezones-java-ja
https://zenn.dev/akkie1030/articles/javascript-dayjs
https://zenn.dev/cloudbase/articles/1fec905b14fde7
https://zenn.dev/dmikurube/articles/curse-of-timezones-common-ja
