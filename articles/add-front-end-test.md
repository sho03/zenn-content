---
title: "Reactのテストを追加する"
emoji: "🐡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react"]
published: false
---

## 　この記事は
フロントエンド（React）のテストを追加した際の備忘録。

## テストを追加するきっかけ
`react-router-dom`などのライブラリをバージョンアップしたいが、テストがなかったため挙動が変わっていないことを保証できない。
`react-router-dom`は画面の遷移などを受け持つライブラリなので、あるURLに対する画面描画が正しいかのみテストを書く。

## 環境
| FW/ライブラリ | Head | Head |
| ---- | ---- | ---- |
| React | 17 | Text |
| react-redux | Text | Text |
| react-router-dom | Text | Text |

## テストを書く

## Routerでラップする
`<Link>`や`<Route>`を利用しているコンポーネントをテストする場合、`<MemoryRouter>`などでテスト対象コンポーネントをラップする必要がある。
```javascript
test("renders learn react link", () => {
    const { getByText } = render(
        <BrowserRouter>
            <MyComponent/>
        </BrowserRouter>
    );
});
```

## React Router v5
https://testing-library.com/docs/example-react-router/#testing-library-and-react-router-v5

## react-testing-libraryとReactv17
https://zenn.dev/msksgm/articles/20220506-testing-library-react-v13-to-react-v17