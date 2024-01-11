---
title: "Spring Boot 2.7から3系へ"
emoji: "👏"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["springboot", "spring"]
published: false
publication_name: "welmo"
---

## この記事は
Spring Bootを2.7から3系へあげた際のメモ書きです。
具体的には以下のライブラリ/ツールをバージョンアップしました。

| ライブラリ | before | after |
| ---- | ---- | ---- |
| Spring | 5.3.20 | 6.1.2 |
| Spring Boot | 2.7.0 | 3.2.1 |
| Spring Security | 5.7.1 | 6.2.1 |
| flyway | 6.4.0 | 9.0.0 |
| exposed | 0.30.1 | 0.46.0 |
| Gradle | 7.3 | 7.5 |
| springdoc | 1.5 | 2.3.0 |

## 公式ドキュメント
https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0-Migration-Guide
2.7.xから3.0へのマイグレーションガイドが公式から出ているので、大抵の場合はこれに沿って実践すれば良いと思います。

## Spring
### URL Matching Changes
[Spring MVC and WebFlux URL Matching Changes](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0-Migration-Guide#spring-mvc-and-webflux-url-matching-changes)

```kotlin
@GetMappign("/some/greeting")
fun greeting() {
    return "hello"
}
```
上記のコードは、`GET /some/greeting/`では合致しなくなり、`404`のレスポンスとなります。

## Jakarta EE
Spring validationなど、`javax`のパッケージ名を`jakarta`に変更します。
```diff java:Sample.kt
- import javax.validation.Valid
+ import jakarta.validation.Valid
```

## Spring Security
### マイグレーションガイド
以下を参考にマイグレーションします。
https://docs.spring.io/spring-security/reference/6.0/migration/index.html

また、Qiitaの記事も大変参考になります。
https://qiita.com/suke_masa/items/908805dd45df08ba28d8

### EnablMethodSecurity
https://docs.spring.io/spring-security/reference/servlet/authorization/method-security.html#migration-enableglobalmethodsecurity

```diff kotlin
- @EnableGlobalMethodSecurity(prePostEnabled = true)
+ @EnableMethodSecurity(prePostEnabled = true)
```

### requestMatchersを使う
https://docs.spring.io/spring-security/reference/5.8/migration/servlet/config.html#use-new-requestmatchers

```diff kotlin
- http.authorizeRequests()
-     .mvcMatchers(anyPath).permitAll()
+ http.authorizeHttpRequests()
+     .requestMatchers(anyPath).permitAll()
```

### XSS protection
HTTP headerのX-XSS-Protectionのデフォルト値が変更になっています。
テストコードでヘッダのアサーションをしていたため、修正しました。
https://github.com/spring-projects/spring-security/issues/9631

### その他
リリース日は未定のようですが、すでに7.0の予告もされています。
https://docs.spring.io/spring-security/reference/6.1/migration-7/index.html

## Kotlin Exposed 
パッケージ名が`java-time`から、`javatime`に変更されているため、変更が必要です。
[Change Log](https://github.com/JetBrains/Exposed/blob/main/docs/ChangeLog.md#0361)

## Flyway
https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0-Migration-Guide#flyway
`Flyway 8.2`より、MySQLドライバーがFlyway本体に含まれなくなっているので、別途dependenciesに含める必要があります。以下、[Release Notes](https://documentation.red-gate.com/fd/release-notes-for-flyway-engine-179732572.html)の8.2.0より引用。
> Removing MySQL Driver from inclusion in Flyway distribution due to License. MariaDB will be used as fallback driver if no MySQL driver is present on the Classpath.

例えば、MySQLの場合は以下のようになります。
```diff groovy:build.gradle
// Flyway
implementation "org.flywaydb:flyway-core:$flywayVersion"
+ implementation "org.flywaydb:flyway-mysql:$flywayVersion"
```

なお、`0.46.0`では、`select`メソッドが非推奨になっているため、将来的に修正の必要があります。（関連する[issue](https://github.com/JetBrains/Exposed/pull/1916)）

他に、[Flyway 7 Breaking Changes](https://github.com/flyway/flyway/issues/2705)も参照。

## Gradle
Gradleはwrapperを利用していれば以下のコマンドを実行するだけで十分です。
```
$ ./gradlew wrapper --gradle-version=7.5
```

## springdoc-openapi 
Spring Boot 3系は、springdoc-openapi 2.0.xと利用できるので、springdoc-openapiもバージョンアップします。
https://springdoc.org/faq.html#_what_is_the_compatibility_matrix_of_springdoc_openapi_with_spring_boot

```diff groovy
+    implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.3.0'
```

## 他に参考になった資料
https://speakerdeck.com/line_developers/findings-in-migrating-our-application-to-spring-boot-3-dot-0
