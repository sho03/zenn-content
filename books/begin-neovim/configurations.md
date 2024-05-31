---
title: "各種設定"
---

Neovimは初期状態では行番号が表示されていないなどの不便な点が多く残っています。
そこで、設定ファイルに好みの設定を記述していき、使いやすくしていきましょう。

## config.luaの作成
以下、長くなりますが`config.lua`ファイルの一例です。

:::details config.lua
```lua:config.lua
vim.cmd("set expandtab")
vim.cmd("set tabstop=2")
vim.cmd("set softtabstop=2")
vim
vim.cmd("set shiftwidth=2")
vim.g.mapleader = " "

local opt = vim.opt

opt.number = true
opt.cursorline = true
opt.relativenumber = false

-- show invisible character
opt.list = true
opt.listchars = { tab = '>>', trail = '-', nbsp = '+' }

opt.clipboard= 'unnamedplus'

opt.termguicolors = true
```
:::

vimでは、カーソル移動の際に数字を入力してから移動キーを入力することで入力した数字分の移動が可能になるので、以下のように現在のカーソル行との相対位置を表示するようにすると移動の際に楽になるかもしれません。
```lua
opt.relativenumber = true
```
