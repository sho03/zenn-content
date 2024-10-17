---
title: "Auth0のManagementAPIでユーザーを作成する"
emoji: "📝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['auth0', 'kotlin']
published: false
---
## この記事は
Auth0の[Management API](https://auth0.com/docs/api/management/v2)を使用してユーザーを作成します。

## アクセストークンの取得
https://auth0.com/docs/secure/tokens/access-tokens/management-api-access-tokens#get-management-api-tokens

Management APIを事項するために、はじめにアクセストークンを取得します。
大きく分けて以下の3種類がありますが、今回はProduction用のアクセストークン取得を行います。
簡易的に試したいのであれば、Testingで十分と思います。
* [Testing](https://auth0.com/docs/secure/tokens/access-tokens/management-api-access-tokens/get-management-api-access-tokens-for-testing)
* [Production](https://auth0.com/docs/secure/tokens/access-tokens/management-api-access-tokens/get-management-api-access-tokens-for-production)
* [Single page applications (SPAs)](https://auth0.com/docs/secure/tokens/access-tokens/management-api-access-tokens/get-management-api-tokens-for-single-page-applications)


![](https://storage.googleapis.com/zenn-user-upload/51b221dad0aa-20240704.png)

設定が完了したら、一度`curl`でトークン取得エンドポイントが成功するか試してみます。

```shell
curl
```

## バックエンドの実装
`curl`で成功したそれぞれの処理をバックエンドに実装していきます。
例では`RestTemplate`を使用していますが、外部APIの実行をしているだけなので利用しているFWや言語に合わせていただければと思います。

```kotlin:Auth0Configuration.kt
@Configuration
@ConfigurationProperties(prefix = "okta.oauth2")
open class Auth0Configuration {

    lateinit var issuer: String
    lateinit var clientId: String
    lateinit var clientSecret: String
    lateinit var audience: String
    lateinit var managementApiUri: String
}
```

```kotlin:Auth0GatewayImpl
@Component
class Auth0GatewayImpl(
    private val auth0Configuration: Auth0Configuration
) : Auth0Gateway {

    // アクセストークン取得処理
    private fun getAccessToken(): String {
        RestTemplateBuilder().build().postForObject(
            "${auth0Configuration.issuer}oauth/token",
            mapOf(
                "client_id" to auth0Configuration.clientId,
                "client_secret" to auth0Configuration.clientSecret,
                "audience" to auth0Configuration.audience,
                "grant_type" to "client_credentials"
            ),
            Map::class.java
        )?.let {
            return it["access_token"] as String
        } ?: throw RuntimeException("Failed to get access token")
    }

    // ユーザー登録
    override fun register(email: Email, password: String) {
        val token = getAccessToken()
        val requestBody = RegisterUserRequest(email.value, password, "Username-Password-Authentication")
        val url = "${auth0Configuration.managementApiUri}users"

        val headers = HttpHeaders().also {
            it.setBearerAuth(token)
            it.contentType = MediaType.APPLICATION_JSON
            it.accept = listOf(MediaType.APPLICATION_JSON)
        }

        val requestEntity: HttpEntity<RegisterUserRequest> = HttpEntity(requestBody, headers)

        val restTemplate = RestTemplateBuilder().build()
        val response: ResponseEntity<String> = restTemplate.postForEntity(url, requestEntity, String::class.java)

        if (response.statusCode.is2xxSuccessful.not()) {
            throw RuntimeException("Failed to register user")
        }
    }

    data class RegisterUserRequest(
        val email: String,
        val password: String,
        val connection: String
    )
}
```

```yml:application.yml
okta:
  oauth2:
    issuer: ${OAUTH2_ISSUER_URI:https://your-domain.jp.auth0.com/}
    client-id: ${OAUTH2_CLIENT_ID:xxxxxxxxxxxx}
    client-secret: ${OAUTH2_CLIENT_SECRET:xxxxxxxxxxxxx}
    audience: ${OAUTH2_ISSUER_URI:https://your-domain.jp.auth0.com/}api/v2/
    management-api-uri: ${OAUTH2_ISSUER_URI:https://your-domain.jp.auth0.com/}api/v2/
```

## 参考
https://auth0.com/docs/api/management/v2/users/post-users
https://auth0.com/docs/secure/tokens/access-tokens/management-api-access-tokens/get-management-api-access-tokens-for-production#prerequisites
