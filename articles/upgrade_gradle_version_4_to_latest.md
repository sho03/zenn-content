---
title: "Gradleを4.10から8.4までバージョンアップした時のメモ"
emoji: "🐈"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["gradle"]
published: false
---

# 
プロジェクトの状態は、gradle wrapperを使用している

# コマンド
[公式ドキュメント](https://docs.gradle.org/current/userguide/gradle_wrapper.html#sec:upgrading_wrapper)にあるように、以下のコマンドを実行する。
```
$ ./gradlew wrapper --gradle-version 5.0
```

# 発生するワーニングなどへの対処
例えば、バージョンアップを実行すると、以下のようなワーニングが発生する。
```
Deprecated Gradle features were used in this build, making it incompatible with Gradle 5.0.
Use '--warning-mode all' to show the individual deprecation warnings.
See https://docs.gradle.org/4.10.3/userguide/command_line_interface.html#sec:command_line_warnings
```
バージョンアップ後のGradleでは非推奨になっている機能を利用している場合に出る。上記の通り、URLの内容に沿って詳細なワーニングを出力することで修正の手助けになる。
例えば以下のようにする。
```
$ ./gradlew build --warning-mode all
```

# 参考
Gradleのデフォルト言語がKotlinになっているらしい。
https://blog.jetbrains.com/kotlin/2023/04/kotlin-dsl-is-the-default-for-new-gradle-builds/
>Gradle announced that they have chosen the Kotlin DSL as the default for new Gradle builds!
https://blog.gradle.org/kotlin-dsl-is-now-the-default-for-new-gradle-builds 