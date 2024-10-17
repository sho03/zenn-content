---
title: "React Routerでロールごとに制限されたページを作成する"
emoji: "🙌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react", "reactrouter"]
published: false
---
## この記事は
[`React Router`](https://reactrouter.com/en/main)を使用して、ユーザーが持つロールごとに制限されたルートを作成します。

## ユーザー情報
ユーザーにロール情報を持たせます。

```js
type User = {
  name: string,
  role: Role
}

type Role = "user" | "admin"
```

## ユーザー情報をContextで保持する
`useContext`を使用してユーザー情報をアプリケーション全体で管理します。
（この辺は状態管理ライブラリを使用しても大丈夫と思います。）

```js
de, createContext, useContext, useEffect, useState } from "react";

// TODO enum ?
export enum RegistrationStatus {
  PENDING = "pending",
  REGISTERED = "registered",
  UNREGISTERED = "unregistered"
}

export type AuthState = {
  user: User | null,
  registrationStatus: RegistrationStatus
}

export const RegistrationStatusContext = createContext<AuthState>({user: null, registrationStatus: RegistrationStatus.PENDING});

export const useRegistrationStatus = (): AuthState => useContext(RegistrationStatusContext);

type Props = {
  children: ReactNode
};

export const RegistrationStatusProvider = (props: Props) => {
  const { children } = props;

  const [registrationStatus, setRegistrationStatus] = useState<RegistrationStatus>(RegistrationStatus.PENDING);
  const [user, setUser] = useState<User | null>(null);

  useEffect(() => {
    fetch('/api/auth')
      .then(async (response) => await response.json())
      .then(json => {setUser(json);})
      .catch(() => setRegistrationStatus(RegistrationStatus.UNREGISTERED))
  }, [])

  return <RegistrationStatusContext.Provider value={{user, registrationStatus}}>{ children }</RegistrationStatusContext.Provider>;
}
```

## 制限ルートを作成する
本題の制限ルートの作成方法に入ります。
```js
import React from "react"
import { RegistrationStatus, useRegistrationStatus } from "../features/auth/RegistrationStatusProvider"
import { Navigate } from "react-router-dom";

export function AdminRouter(props: Props) {

  const authUser = useRegistrationStatus();
  const { user, registrationStatus } = authUser;

  console.log(user);

  if (!user) {
    return <Navigate to={"/"} />
  }

  if (user?.role !== "admin") {
    return <Navigate to={"/"} />
  }

  if (registrationStatus === RegistrationStatus.UNREGISTERED) {
    return <Navigate to={"/login"} />
  }

  if (registrationStatus === RegistrationStatus.PENDING) {
    return <Navigate to={"/login"} />
  }

  return (
    <>
      {props.component}
    </>
  )

}

type Props = {
  component: React.ReactNode,
}

```

## 参考
https://zenn.dev/longbridge/articles/61b05d8bdb014d
https://github.com/remix-run/react-router/tree/dev/examples/auth
