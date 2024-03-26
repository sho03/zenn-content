---
title: "Neovimを使い始める"
emoji: "👏"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["vim", 'neovim']
published: false
---

## この記事は
テキストエディタの`Neovim`を使い始めるまでのメモです。vimの使い方やvimとの違いなどについては触れません。
筆者は Neovimはおろか、VimすらGitのコミットメッセージを書く時程度にしか使用していませんので、誤りや不明瞭な記載があるかもしれないことをご留意ください。

## インストール
インストール方法は[こちら](https://github.com/neovim/neovim/wiki/Installing-Neovim)に色々ありますが、今回は`Homebrew`を例に載せています。
```shell
$ brew install neovim
```

```
:h key-notation
```

```
$ vimtutor
```
`nvim`で、 Neovimが起動するか試してみましょう。
![](https://storage.googleapis.com/zenn-user-upload/84cadc467b56-20240127.png)

## keymaps

## 参考
https://lesguillemets.github.io/blog/2014/10/16/vim-window-controls.html
https://zenn.dev/hisasann/articles/neovim-settings-to-lua

awesome-neovimというリポジトリでがイカしたプラグインをまとめている
https://github.com/rockerBOO/awesome-neovim

fuzzy finder
https://zenn.dev/kobakichi/articles/c9e1e740f5f5b4
