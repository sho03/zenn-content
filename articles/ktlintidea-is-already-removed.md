---
title: "ktlintApplyToIdeaの代わりに.editorconfigを使う"
emoji: "😸"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['ktlint', 'kotlin']
published: false
---
## この記事は
[ktlint-gradle plugin](https://github.com/JLLeitschuh/ktlint-gradle)からはすでに`KtlintIdea`プラグインが[削除されており](https://github.com/JLLeitschuh/ktlint-gradle/pull/726)、`ktLintApplyToIdea`タスクが使用できなくなっています。
IDEのプラグインとGradleタスクのフォーマッタを揃えるために、代わりに`.editorconfig`を使用します。


## .editorconfigを作成する
プロジェクトのルートに`.editorconfig`を作成してそこでルールを定義します。
今回修正したかったルールが[関数式への自動修正](https://pinterest.github.io/ktlint/1.3.1/rules/standard/#function-expression-body)だったので、それを無効化します。

```txt:.editorconfig
[*.{kt,kts}]
ktlint_standard_function-expression-body = disabled
```

これでIDEのktlintプラグインでもgradleタスクのktlintFormatでも同じルールが適用されます。

## 余談
IDEのプラグインの設定と、Graldeで定義している`ktlint`のルールが統一されていなかったため、開発者によってフォーマッタの結果が異なっていました。
こういうのは最初にきっちりするようにします。

## 参考
https://pinterest.github.io/ktlint/0.49.1/rules/configuration-ktlint/
https://github.com/JLLeitschuh/ktlint-gradle/pull/726
https://pinterest.github.io/ktlint/1.3.1/rules/standard/#function-expression-body
https://github.com/JLLeitschuh/ktlint-gradle/releases/tag/v12.0.2
https://bsblog.casareal.co.jp/archives/2333
