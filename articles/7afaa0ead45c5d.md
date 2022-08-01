---
title: "Github Actionsで自動でtagを作成する"
emoji: "🌟"
type: "tech"
topics: []
published: false
---

# GitHub Tagを使用する
[GitHub Tag](https://github.com/marketplace/actions/github-tag)を利用するのが一番簡単に思います。
以下はUsageそのまま。これを`.github/workflow`に`bump_version.yml`とでも作成しておくと、`main`ブランチへのpushでタグが自動で作成されます。
```yaml
name: Bump version
on:
  push:
    branches:
      - main
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Bump version and push tag
        id: tag_version
        uses: mathieudutour/github-tag-action@v6.0
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
      - name: Create a GitHub release
        uses: ncipollo/release-action@v1
        with:
          tag: ${{ steps.tag_version.outputs.new_tag }}
          name: Release ${{ steps.tag_version.outputs.new_tag }}
          body: ${{ steps.tag_version.outputs.changelog }}
```

# 問題点
上記のまま実行すると、プレフィクスに該当する全てのコミットがリリースノートに記載されてしまいます。

## 改善（リリースノートの文面を変更する）
リリースノートに各トピックブランチのプルリクエストのタイトル及びリンクをリリースノートに記載することにします。

### プルリクエストの番号のみを取得する
```
$ g log --grep 'Merge pull request' --oneline | awk '{print $5}'
```

### 特定の2タグ間のコミットログを取得する
```
$ g log --pretty=oneline v0.0.1...v1.0.0
```

### 2タグ間のプルリクエストの番号を取得する
```
$ g log --pretty=oneline v0.0.1...v1.1.2 | grep 'Merge pull request' | awk '{print $5}'
```

### pull requestの番号を取得する
https://stackoverflow.com/questions/59077079/how-to-get-pull-request-number-within-github-actions-workflow
```
env:
  PR_NUMBER: ${{ github.event.number }}
```

`gh`コマンドを使用する方が良いかもしれない
```
$ gh pr list -s closed

# 以下のような内容が出力される
Showing 10 of 10 pull requests in sho03/github_actions_test that match your search

#10  Develop                  develop
#9   feat: add new_text1.txt  feature/feature_new
#8   Fix/fix b                fix/fix_B
#7   feat: add feature_A.txt  feature/test_A
#6   Develop                  develop
#5   hogehoge                 feature/create_text2
#4   create text              feature/create_text
#3   Fix/bug                  fix/bug
#2   Feature/branch b         feature/branch_b
#1   Feature/branch a         feature/branch_a
```