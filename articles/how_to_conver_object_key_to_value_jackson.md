---
title: "JSONのkeyをvalueとして使いたい"
emoji: "📚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["kotlin", "jackson", "SpringBoot"]
published: false
---

# やりたいこと
以下の様なJSONを
```json
{
    "userId0001": {
        "name": "taro",
        "age": 20
    }
}
```
このようなデータクラスにパースする
```kotlin
data class User(
    val userId: UserId,
    val name: String,
    val age: Int
)
```

# Spring BootでJacksonを扱う場合の基本
`Spring Boot`でコントローラに`@RestController`アノテーションを使用すると、返り値の方にオブジェクトを指定した場合に自動でJSONに変換してレスポンスされます。

## 単純なサンプル
下記のサンプルは、`/hello`にアクセスするとMessageオブジェクトがJSONとしてレスポンスされます。
```kotlin
@RestController
class SampleController {

    @GetMapping("/hello")
    fun hello(): ResponseEntity<Message> {
        val hello = Message("hello")
        return ResponseEntity.ok(hello)
    }

    data class Message(
        val message: String
    )
}

```

```shell
$ curl localhost:8888/hello
{"message":"hello"}% 
```

## JsonComponentを使用する例

# 結論