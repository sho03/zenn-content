---
title: "スプレッドシートAPIのエラーレスポンス"
emoji: "🐷"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---
## この記事は

## レスポンス色々
### 400
対象のシートが存在しない or Rangeの指定が誤っている
```
{
  "code": 400,
  "errors": [
    {
      "domain": "global",
      "message": "Unable to parse range: 2024/12/18",
      "reason": "badRequest"
    }
  ],
  "message": "Unable to parse range: 2024/12/18",
  "status": "INVALID_ARGUMENT"
}
```
### 403
対象のスプレッドシートから利用するサービスアカウントの参照権限を外す
```
{
  "code": 403,
  "errors": [
    {
      "domain": "global",
      "message": "The caller does not have permission",
      "reason": "forbidden"
    }
  ],
  "message": "The caller does not have permission",
  "status": "PERMISSION_DENIED"
}
```

### 404
存在しないスプレッドシートのIDを指定してAPIを実行する
```
{
  "code": 404,
  "errors": [
    {
      "domain": "global",
      "message": "Requested entity was not found.",
      "reason": "notFound"
    }
  ],
  "message": "Requested entity was not found.",
  "status": "NOT_FOUND"
}
```




## 参考
