---
title: "Spring Boot 2.7から3系へ"
emoji: "👏"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["springboot", "spring"]
published: false
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

## 公式ドキュメント
https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0-Migration-Guide
2.7.xから3.0へのマイグレーションガイドが公式から出ているので、大抵の場合はこれに沿って実践すれば良いと思います。

## Spring Boot
### URL Matching Changes
[Spring MVC and WebFlux URL Matching Changes](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0-Migration-Guide#spring-mvc-and-webflux-url-matching-changes)

```kotlin
@GetMappign("/some/greeting")
fun greeting() {
    return "hello"
}
```
上記のコードは、`GET /some/greeting/`では合致しなくなり、`404`のレスポンスとなります。

## JakartaEE
spring validationなど、`javax`のパッケージ名を`jakarta`に変更します。
```diff java:Sample.kt
- import javax.validation.Valid
+ import jakarta.validation.Valid
```

## Spring Security
https://docs.spring.io/spring-security/reference/6.0/migration/index.html

https://qiita.com/suke_masa/items/908805dd45df08ba28d8

https://docs.spring.io/spring-security/reference/servlet/authorization/method-security.html#migration-enableglobalmethodsecurity

https://docs.spring.io/spring-security/reference/5.8/migration/servlet/config.html#use-new-requestmatchers

https://github.com/spring-projects/spring-security/issues/9631

## Kotlin Exposed 
https://github.com/JetBrains/Exposed/tree/main/exposed-spring-boot-starter
パッケージ名が`java-time`から、`javatime`に変更されているため、変更が必要。
[commit](https://github.com/JetBrains/Exposed/commit/2cbc8080e4788035735a3cfa9d9406a414f49387)
[ChangeLog](https://github.com/JetBrains/Exposed/blob/main/docs/ChangeLog.md#0361)

## flyway
https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0-Migration-Guide#flyway
[Flyway 7 Breaking Changes](https://github.com/flyway/flyway/issues/2705)
`Flyway 8.2`より、MySQLドライバーがFlyway本体に含まれてなくなっているので、別途dependenciesに含める必要がある。
以下、[Release Notes](https://documentation.red-gate.com/fd/release-notes-for-flyway-engine-179732572.html)の8.2.0より引用。
> Removing MySQL Driver from inclusion in Flyway distribution due to License. MariaDB will be used as fallback driver if no MySQL driver is present on the Classpath.

`0.46.0`では、`select`メソッドが非推奨になっているため、修正の必要があります。（関連する[issue](https://github.com/JetBrains/Exposed/pull/1916)）

## Gradle
Gradleはwrapperを利用していれば以下のコマンドを実行するだけで十分です。
```
$ ./gradlew --version 

$ ./gradlew wrapper --gradle-version=7.5
$ ./gradlew --version

```

## 参考
https://speakerdeck.com/line_developers/findings-in-migrating-our-application-to-spring-boot-3-dot-0
