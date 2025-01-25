---
title: "Jacksonのアノテーションいろいろ"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---
## この記事は
`Jackson`のアノテーションを色々紹介します.
私は普段Spring Bootアプリケーションを開発しているので、その中でよく出てくるアノテーションになってしまう
## Jackson

### @JsonPropertyでプロパティとJsonキーの違いを吸収する
```
data class Person(
    @JsonProperty("full_name")
    val fullName: String,
)
```

```json:sample.json
{ "full_name": "taro suzuki "}
```

### @JsonGetter / @JsonSetter
`JsonGetter`, `JsonSetter`を使うことで、シリアライズ・デシリアライズ時それぞれのJsonキーを指定することもできます。

### @JsonAlias

### 

## 参考
