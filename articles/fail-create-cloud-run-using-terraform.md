---
title: "Cloud Runを作成するのに失敗した時のメモ書き"
emoji: "📑"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["terraform"]
published: false
---

# 発生したエラー
```
##[debug]Error: Error creating Service: googleapi: Error 400: metadata.annotations[run.googleapis.com/vpc-access-connector]: Annotation 'run.googleapis.com/vpc-access-connector' is not supported on resources of kind 'Service'. Supported kinds are: Revision, Execution
```

```diff tf:sample.tf
+ autogenerate_revision_name = true
```
