---
title: "Material UIを使ってみる"
emoji: "🎃"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react", "materialui"]
published: false
---

## この記事は
Material UIを導入した時の記録です。
基本的に公式の[Installation](https://mui.com/material-ui/getting-started/installation/)などを実施したものになります。

## インストール
Material UIに必要なモジュールをインストールします。
```
npm install @mui/material @emotion/react @emotion/styled
```

Material UIがデフォルトで使用するRobotoというフォントをインストールします。
```
npm install @fontsource/roboto
```
`App.tsx`でインポートします。
```typescript
import '@fontsource/roboto/300.css';
import '@fontsource/roboto/400.css';
import '@fontsource/roboto/500.css';
import '@fontsource/roboto/700.css';
```

アイコンを利用するためのモジュールもインストールします。
```
npm install @mui/icons-material
```

## ボタンコンポーネント
公式の[Usage](https://mui.com/material-ui/getting-started/usage/)をそのまま拝借して、コンポーネントを作成します。
```
import * as React from 'react';
import Button from '@mui/material/Button';

export default function ButtonUsage() {
  return <Button variant="contained">Hello world</Button>;
}
```

