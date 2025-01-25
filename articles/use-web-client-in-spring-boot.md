---
title: "SpringでRestクライアントを使う"
emoji: "📘"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['spring']
published: false
---
## この記事は
いつの間にかSprinが提供しているRestクライアントが増えていたのでまとめることにしました。
https://spring.pleiades.io/spring-framework/reference/integration/rest-clients.html

## 結論
個人的にはこんな感じかと思います。
| クライアント | 所感 | 追加バージョン |
| ---- | ---- | ---- |
| `WebClient` | まずはこれを使うことを考える。非同期・ノンブロッキングであることを謳っているが、同期的にも使用できるので、`RestClient`の代替としても利用できる。 | Text |
| `RestClient` | 同期的なRestクライアント。`RestTemplate`の代替としてモダンな機能を取り入れたもの。`WebClient`の同期処理バージョンと考えればよい。 | Text |
| `RestTemplate` | 基本的には使わない。少なくとも新規開発においては選択肢に入れなくて良い。現時点では`deprecated`になっているわけでもないので、急いで上記のクライアントに変更しなくても問題ない。ただし、新規機能が追加されないことは留意すること。 | Text |

## 実装例

```kotlin:sample.kt
@Component
class RestClientSample(
    private val restTemplate: RestTemplate,
    private val restClient: RestClient,
    private val webClient: WebClient,
) {

    fun getTodoWithRestClient(id: Int): Todo {
        val todo = restClient
            .get()
            .uri("https://jsonplaceholder.typicode.com/todos/{id}", id)
            .retrieve()
            .onStatus(HttpStatusCode::isError) { _, response ->
                throw RuntimeException("failed get request ${response.statusCode}")
            }
            .body<Todo>()
        if (todo == null) throw RuntimeException("body is empty.")
        return todo
    }

    fun getTodoWithWebClient(id: Int): Mono<Todo> {
        val result = webClient
            .get()
            .uri("https://jsonplaceholder.typicode.com/todos/{id}", id)
            .retrieve()
            .onStatus({status -> status.isError}) { response ->
                Mono.error(RuntimeException("failed get request ${response.statusCode()}"))
            }
            .bodyToMono<Todo>()

        return result
    }

    fun getTodoWithRestTemplate(id: Int): Todo {
        val result = restTemplate.getForEntity("https://jsonplaceholder.typicode.com/todos/{id}", Todo::class.java, id)
        if (result.statusCode.isError) {
            throw RuntimeException("failed get request ${result.statusCode}")
        }
        val body = result.body ?: throw RuntimeException("body is null")

        return body
    }

    data class Todo(
        val userId: Int,
        val id: Int,
        val title: String,
        val completed: Boolean
    )
}
```

### WebClient
`WebClient`を使用した際に注目すべきは戻り値の型が`Mono`もしくは`Flux`になることでしょう。

### RestClient

## コンポーネントに切り出す
ここからは余談ですが、`WebClient`や`RestClient`

## 非同期・ノンブロッキング

## 参考
https://www.baeldung.com/spring-webclient-resttemplate
https://spring.pleiades.io/spring-framework/reference/integration/rest-clients.html
