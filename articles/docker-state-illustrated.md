---
title: "Dockerコマンドおさらい"
emoji: "🌟"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["docker"]
published: false
---

# はじめに
Dockerのコマンドの種類とそれらを実行したときの結果の一覧が欲しかったため、作成しました。
各コマンドの解説も後述します。

```mermaid
flowchart LR

image
repo[Docker Repository]
file[Dockerfile]

file --> |build| image
repo --> |pull| image
image --> |push| repo
image --> |run| up
image --> |create| down

subgraph "コンテナ"
    up["起動中コンテナ"]
    down["停止中コンテナ"]
    up --> |stop| down
    down --> |start| up
end

subgraph Dockerfile
    file
end

subgraph DockeHub
    repo
end

subgraph イメージ
    image
end

```

# 各種コマンド解説
## build
`Dockerfile`をもとにイメージを作成します。
例
```shell
$ docker build .
```

### オプションなど
#### -t

#### -f

## push
例
```shell
$ docker build -t 
```

## pull
例
```shell
$ docker build -t 
```

## create
例
```shell
$ docker build -t 
```

## run
例
```shell
$ docker run image
```
`docker run`は、前述した`docker create`と`docker start`を実行することと同じです。

## stop
例
```shell
$ docker build -t 
```

## start
例
```shell
$ docker build -t 
```