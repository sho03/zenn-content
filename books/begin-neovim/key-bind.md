---
title: "キーバインド"
---

次にキーバインドの設定もしていきましょう。
`keymaps.lua`というファイルを作成します。以下はサンプルです。

```lua:keymaps.lua
local keymap = vim.keymap

keymap.set("", "<Space>", "<Nop>")
vim.g.mapleader = " "
vim.g.maplocalleader = " "

-- to normal mode.
keymap.set('i', 'jk', '<ESC>')

-- split window
keymap.set("n", "ss", ":split<Return><C-w>w")
keymap.set("n", "sv", ":vsplit<Return><C-w>w")

-- increment/decrement
keymap.set("n", "+", "<C-a>")
keymap.set("n", "-", "<C-x>")

-- moving
keymap.set("n", "sh", "<C-w>h")
keymap.set("n", "sj", "<C-w>j")
keymap.set("n", "sk", "<C-w>k")
keymap.set("n", "sl", "<C-w>l")

-- move tab
keymap.set("n", "gh", "gT")
keymap.set("n", "gl", "gt")
-- open new tab
keymap.set("n", "te", ":tabedit<Return>")

-- move line start/end
keymap.set("n", "<Space>h", "^")
keymap.set("n", "<Space>l", "$")

-- begin command to press ';'
keymap.set("n", ";", ":")
```

個人的に好きのは、
```
-- to normal mode.
keymap.set('i', 'jk', '<ESC>')
```
これで、`j`と`k`を連続で入力することでインサートモードからノーマルモードへ簡単に移行できます。
ESCキーを押さなくて済むので、Neovimで主に編集することが多い時は体感でかなり指の動きが減るように思います。
