---
title: "React Reduxアプリにテストを追加する"
emoji: "🐙"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react", "redux", "test"]
published: false
---


# この記事は
`react` + `redux`アプリに自動テストを追加した際の記録

# はじめにやる事
`App.test.tsx`を、`src/__test__`ディレクトリに移動する。
`App.test.tsx`は`create-react-app`を利用した際に作成されたものなのだが、コードを以下に示す。

```js:App.test.tsx
import React from "react";
import { render } from "@testing-library/react";
import App from "../App";

test("renders learn react link", () => {
    const { getByText } = render(<App/>);
    const linkElement = getByText(/learn react/i);
    expect(linkElement).toBeInTheDocument();
});

```

`yarn test`でテストを実行すると以下のエラーが発生。
```
 FAIL  src/__test__/App.test.tsx
  ✕ renders learn react link (69 ms)

  ● renders learn react link

    could not find react-redux context value; please ensure the component is wrapped in a <Provider>
```

エラー通り、`Provider`で囲む。
また、実際にテストを実施している箇所は一旦コメントアウトする（失敗するので）
```js:App.test.tsx
import React from "react";
import { render } from "@testing-library/react";
import App from "../App";
import { Provider } from "react-redux";
import { Store } from "../state/store";

test("renders learn react link", () => {
    const { getByText } = render(
        <Provider store={Store}>
            <App/>
        </Provider>
    );
    // const linkElement = getByText(/learn react/i);
    // expect(linkElement).toBeInTheDocument();
});
```
ここまでで、`yarn test`をすることで実際にテストが成功することを確認。

# カスタムレンダー関数の作成
上記のエラーのように、`Redux`を利用する場合はコンポーネントを`<Provider>`で囲む必要があります。テストごとに都度書いても良いのですが、`Redux`の公式ページには便利な関数を作成する例が載っています。
[Setting Up a Reusable Test Render Function](https://redux.js.org/usage/writing-tests#setting-up-a-reusable-test-render-function)
[Custom Render](https://testing-library.com/docs/react-testing-library/setup/#custom-render)
TODO 

# 色々なテスト
一応テストが実行可能になったので、各種のテストを実施していく。

## タイトルのテスト
タブに表示される文字列が正しいかのテストを実施する。
```js

```

# 参考
https://redux.js.org/usage/writing-tests#ui-and-network-testing-tools
https://mswjs.io/