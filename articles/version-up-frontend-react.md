---
title: "React 17 to 18"
emoji: "🌊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react"]
published: false
---

## この記事は
Reactの18が出てから2年経っているにも関わらずいまだに17のままだったのでバージョンを上げる。

| ライブラリ | before | after |
| ---- | ---- | ---- |
| react | 17.0.1 | Text |
| react-dom | 17.0.1 | Text |
| react-redux | 7.2.0 | Text |
| react-router-dom | 5.2.0 | Text |

## 順序
1. `react`を16.8以上にあげる
1. `react-router-domを`v6に上げる
1. `react`を18に上げる
1. `react-redux`を8以上にあげる

## react-router-dom
`react-router-dom`が5.2の場合、Reactを18に上げると以下のエラーが発生する。
```
'BrowserRouter' cannot be used as a JSX component.
```
* https://github.com/remix-run/react-router/issues/8794

* https://github.com/remix-run/react-router/issues/7870

また、以下の記事も参考になる。
https://developers.matsuri.tech/posts/upgrading-react-to-v18#BrowserRouter%E3%81%A7%E5%9E%8B%E3%82%A8%E3%83%A9%E3%83%BC%E3%81%8C%E5%87%BA%E3%82%8B

### 公式ページを元にアップグレードする
https://reactrouter.com/en/main/upgrading/v5#upgrading-from-v5

`react-router-dom-v5-compat`を使用すると、v5とv6を並行して動作させながら安全にv6へのアップグレードができる模様。
全て終わり次第、`react-router-dom`をv6にすれば良いみたい。
https://github.com/remix-run/react-router/discussions/8753

`useNavigate()`を利用した際に、遷移先の画面に値を渡す方法
https://zenn.dev/horisan/articles/2aeaf0bd3fb70f

### エラー
```
[PrivateRouter] is not a <Route> component. All component children of <Routes> must be a <Route> or <React.Fragment>
```
https://gist.github.com/mjackson/d54b40a094277b7afdd6b81f51a0393f#route-composition-in-react-router-v6

### useParamsの型が変更になっている.
https://dev.classmethod.jp/articles/react-router-5to6/
https://reactrouter.com/en/main/hooks/use-params
https://github.com/remix-run/react-router/blob/fb0f1f94778f4762989930db209e6a111504aa63/packages/react-router/lib/hooks.tsx#L293
https://github.com/remix-run/react-router/blob/fb0f1f94778f4762989930db209e6a111504aa63/packages/router/utils.ts#L372
```javascript
declare function useParams<K extends string = string>(): Readonly<Params<K>>;

export type Params<Key extends string = string> = {
  readonly [key in Key]: string | undefined;
};
```
`/user/:id`でルーティングしている以上、`id`が`undefined`になる可能性なくないかと思っていたところ、似た考えの質問が存在していた。
https://stackoverflow.com/questions/69992370/why-react-router-v6-useparams-returns-object-with-properties-possibly-undefined

### useLocation
https://zenn.dev/lilac/articles/c40b35fe3184da

## React 
以下の公式アップグレードガイドに沿って実施する。
https://react.dev/blog/2022/03/08/react-18-upgrade-guide

```shell
yarn add react react-dom 
yarn add @types/react @types/react-dom 
```

### toBeInTheDocument()が存在しない？
`Property 'toBeInTheDocument' does not exist on type 'JestMatchers<HTMLElement>'.ts(2339)`
![](https://storage.googleapis.com/zenn-user-upload/0f0cff7f8dff-20240123.png)

https://github.com/testing-library/jest-dom/issues/546#issuecomment-1800436478

### <SomeComponent> cannnot be used as JSX Element
https://zenn.dev/karabiner_inc/articles/103a4337144db3


## react-redux
react-reduxは、v8.0.0からReact18との互換性がある。記事執筆時点の最新版とする。
https://github.com/reduxjs/react-redux/releases/tag/v8.0.0

```shell
yarn add react-redux@latest
yarn add @types/react-redux@latest
```

## その他エラー
以下と同様のエラーが出たので、ディスカッションの中にあった`yarn add @babel/runtime`を実行して解消。
https://github.com/facebook/create-react-app/issues/7183


## 他の参考記事
https://zenn.dev/khale/articles/react-router-update-v6