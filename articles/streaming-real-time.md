---
title: "Google Speech to Textを使ってリアルタイム音声認識アプリを作成する"
emoji: "😊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['gcp', 'js']
published: false
---
## この記事は
GoogleのSpeech to textを使用してリアルタイム音声認識のWebアプリを作成した際のメモ書きです。

## 時間制限に対する対処
ストリーミングの場合、[上限が5分](https://cloud.google.com/speech-to-text/quotas?hl=ja)なので、対策を講じる必要があります。
[無限ストリーミングチュートリアルのコード](https://github.com/GoogleCloudPlatform/nodejs-docs-samples/blob/main/speech/infiniteStreaming.js)を参考にすると、一定時間ごとにストリームを再生成していることがわかります。
以下は抜粋したコードです。
```js
const streamingLimit = 10000;

// Restart stream when streamingLimit expires
setTimeout(restartStream, streamingLimit);

function restartStream() {
  // The implementation details are omitted
}
```

上記のチュートリアルのコードを参考にWebSocketを用いて作成したサーバサイドのコードが以下になります。なお、このコードはうまく動きません。
:::details code
```js
import { WebSocketServer }from 'ws';
import { SpeechClient } from '@google-cloud/speech';

const wss = new WebSocketServer({ port: 8080 });

const client = new SpeechClient();

// const encoding = 'LINEAR16';
const encoding = 'WEBM_OPUS';

const request = {
  config: {
    encoding: encoding,
    sampleRateHertz: 16000,
    languageCode: "ja-JP",
  },
  interimResults: true,
};

let recognizeStream = null;
let timeoutId = null;
const streamingLimit = 20000;

wss.on("connection", (ws) => {
  console.log('web socket connection established');

  function startStream() {
    console.log('start stream')
    clearTimeout(timeoutId);

    recognizeStream = client
      .streamingRecognize(request)
      .on('data', sendWsClient)
      .on('end', () => { console.log('stream ended.') })
      .on('error', err => {
        console.log('Stream error', err)
        if (err.code === 11) {
          restartStream();
        } else {
          console.error('API request error ' + err);
        }
      })
    // Restart stream when streamingLimit expires
    timeoutId = setTimeout(restartStream, streamingLimit);
    console.log('recognizeStream started: ', recognizeStream);
  }
  startStream();

  function restartStream() {
    console.log('Restarting stream')
    if (recognizeStream) {
      console.log('Stopping stream')
      recognizeStream.end();
      recognizeStream.removeListener("data", sendWsClient);
      recognizeStream = null;
    }
    startStream();
  }

  function sendWsClient(data) {
    console.log('Processing data from Google Speech API.')
    console.log(data);
    const text =
      data.results[0] && data.results[0].alternatives[0]
        ? data.results[0].alternatives[0].transcript
        : "";
    console.log("text: ", text);
    console.log(data.results[0])
    if (data.results[0].isFinal) {
      ws.send(
        JSON.stringify({
          text: text,
        }),
      );
    }
  };

  function close() {
    console.log("WebSocket connection closed.");
    clearTimeout(timeoutId);
    if (recognizeStream) {
      console.log("Stopping stream.")
      recognizeStream.end();
      recognizeStream.removeListener('data', sendWsClient);
      recognizeStream = null
    }
  }

  function receiveMessage(message) {
    console.log('Received message from client');
    if (recognizeStream.writable) {
      try {
        recognizeStream.write(message);
        console.log('write message to stream');
      } catch (e) {
        console.log("Failed to write to stream: ", e);
      }
    } else {
      console.log('recognizeStream is not writable.')
    }
  }
  ws.on("message", receiveMessage);
  ws.on("close", close);

  // test
  // setInterval(() => ws.send(JSON.stringify({text: "test text"})), 3000);
});

```
:::

上記のコードではなぜか一度リスタートした以降にストリーミングがされない事象が発生しました。
原因がわからなかったので、とりあえず[stack overflowで質問しました。](https://stackoverflow.com/questions/78548231/google-speech-to-text-on-web-application)

## 原因と修正


## ストリーミングレスポンスについて
[ストリーミングレスポンス](https://cloud.google.com/speech-to-text/docs/speech-to-text-requests?hl=ja#streaming_responses)によればレスポンスの`results.isFinal`が`true`の場合に結果が最終結果であることを示すようです。

なお、以下は何を意味しているかよくわかりません。
> isFinal=true の結果は、書き込みストリームがクローズ（半クローズ）された後にのみ保証されます。

## 参考
https://cloud.google.com/speech-to-text/docs/transcribe-streaming-audio?hl=ja#endless-streaming
https://cloud.google.com/speech-to-text/docs/endless-streaming-tutorial?hl=ja
https://github.com/GoogleCloudPlatform/nodejs-docs-samples/blob/main/speech/infiniteStreaming.js
https://cloud.google.com/speech-to-text/docs/speech-to-text-requests?hl=ja#streaming_responses
