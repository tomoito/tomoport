---
title: 'Reactでstate管理をする方法(useContext編）'
date: '2021-03-07'
tags: ['React', 'state']
draft: false
summary: 'TypeScriptの型の作り方を楽しく学んできます。'
---

# React で state 管理をする方法（useContext 編）

# React の状態管理

UI コンポーネントの実装する上で、状態をどこで管理するかというのはおうねんの悩みであります。
例えば、フォームの入力値、チェックボックスの状態、外部データなどが挙げられます。
単一のコンポーネントであれば、useState で管理するだけで良いのですが、ほとんどのあぷりけーしょんは、親子のコンポーネント関係が構築されます。
その際、通常親コンポーネントから子コンポーネントにデータを渡す際に、props を介して行います。
しかし、ネストが複雑になると props での受け渡しは煩雑になります。

そこで今回は、一番かんたんに実装ができる「useContext」を使ってみます。
Context API を利用すると props を利用せずに下の階層のコンポーネントと
データの共有ができます。

# とりあえず簡単な例を作ってみる。

```javascript
import React,{useContext} from 'react'

function App(){
  const userInfo = React.createContext('hoge')
  return(
    <div>
      <UserInfo.Provider value={}>
      <Child>
      </UserInfo.Provider>
    </div>
    );
}

const Child = () => {
  const info = userContext(userInfo)
  return <div>{userInfo}</div>
}

```

できました！！
これが useContext を使った最もシンプルな例ではないでしょうか。
