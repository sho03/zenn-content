---
title: "Gradleのマルチモジュールプロジェクト"
emoji: "🤖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["gradle", "springboot"]
published: false
---

## この記事は
Gradleでマルチモジュールプロジェクトを作成するときのあれこれ

## モジュール共通の設定
`buildSrc`
https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:build_sources

### 全モジュール共通

### 一部のモジュール共通

### バージョンを変数で指定する

### プラグイン等
```kts:build.gradle.kts
plugins {
	id("org.springframework.boot") version "3.2.3"
	id("io.spring.dependency-management") version "1.1.4"
	kotlin("jvm") version "1.9.22"
	kotlin("plugin.spring") version "1.9.22"
	application
}
```
:::details kotlin()関数
なお`kotlin`は内部的に`id(dependency)`を呼び出しているだけです。
```kotlin:KotlinDependencyExtentions.kt
fun PluginDependenciesSpec.kotlin(module: String): PluginDependencySpec =
    id("org.jetbrains.kotlin.$module")
```
:::
#### plugin.spring

#### kotlin.jvm

## 余談
バージョンを指定しないと、なぜかそんなdependencyはない、というエラーが発生した。
原因はわからない。バージョン指定しない場合の挙動とかを調べたほうが良いかもしれない。

```diff kts:build.gradle.kts
-     implementation("org.springframework.boot:spring-boot-starter-web")

+     implementation("org.springframework.boot:spring-boot-starter-web:${springBootVersion}")
```

## 参考
https://zenn.dev/loglass/articles/6c449ab8a750f2
https://blog.kengo-toda.jp/entry/2021/07/07/214506


