---
title: "Spring BootでのJSONリクエスト"
emoji: "🐥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["kotlin", "springboot", "jackson"]
published: false
---

# この記事で書くこと
`Spring Boot`でのJSONリクエストを受け取る際に、JSONをオブジェクトへ変換（デシリアライズ）する方法をまとめました。
`Spring Boot`でRESTful APIを実現するためのコントローラであることを前提としています。（コントローラクラスに`@RestController`アノテーションが付与されている。）

なお、レスポンス（オブジェクトをJSONへシリアライズする）についてはこの記事では書きません。とはいえ、どちらも同じ`Jackson`の技術を利用しているので、似たようなことはできると思います。

# 基本
基本は以下の2つとなります。
1. コントローラに`@RestController`を付与する
2. メソッドの引数に`@RequestBody`アノテーションを付与する

以下がサンプルのコントローラです。

```kotlin
@RestController
class SampleController() {
    
    @PostMapping("/sample")
    fun sample(
        @RequestBody user: User
    ) {
        // some code
    }
}

data class User(
    val name: String,
    val age: Int
)
```
上記のコントローラに対して以下のリクエストをすると、`sample`メソッドで`User`型としてJSONを受け取れる様になります。
```

```

# 色々な変換方法

## 独自型に変換したい
前述した`User`クラスは、`String`と`Int`という基本データ型のプロパティを持っていましたが、これらが`UserName`や`Age`といった値オブジェクトの場合を考えます。
```kotlin:User
data class User(
    val name: UserName,
    val age: Age
)

data class UserName(
    val value: String
)

data class Age(
    val value: Int
)
```

上記の様な場合は、`@JsonComponent`を付与したコンポーネントを作成し、デシリアライズ時に値オブジェクトに変換する方法があります。

```kotlin

```

## JSONのキーとオブジェクトのプロパティ名が異なる
`@JsonProperty`を使用することで解決できます。
```kotlin
data class User(
    @JsonProperty("user_name")
    val name: UserName,
    val age: Int
)
```
上記の様にすることで、JSONのキー`user_name`が`User`クラスのプロパティ`name`にバインドされます。

## 送られてくるJSONにキーがあったりなかったりする

## トップレベルのキーが値となっている
例えば以下の様なJSONを考えます。

```json
{
    "USER0001": {
        "name": "john",
        "age": 20
    },
    "USER0001": {
        "name": "john",
        "age": 20
    }
}

```
この様な場合は、リクエストを受け取るメソッドの引数の型を`Map<String, User>`として受け取るのが一番楽かと思います。
```kotlin


```
:::message 
おそらく、前述した`@JsonComponent`でもできそうな気がしますが、断念。
:::
