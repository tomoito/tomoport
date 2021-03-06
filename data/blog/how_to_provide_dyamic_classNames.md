---
title: 'React で 動的にclassNameを適用する方法をまとめてみた。'
date: '2021-03-06'
tags: ['react', 'className']
draft: false
summary: 'Reactで動的に className　を適用する方法をまとめてみます。'
---

# React で動的に className を適用する方法をまとめてみます。

# はじめに

React コンポーネントのクラスが違うだけのコンポーネントを複数作っていませんか？
動的にクラス名を指定して、柔軟なコンポーネントを作成しましょう。

# コンポーネントだけで簡潔する

```javascript

const [classNames,setclassNames] = React.useSutate('')
const [showRed, setShowRed] = useState(false)

useEffect(()=> {
    setClassNames(`${showRed ? "red":"" } ${showBlue} ? "blue" :""}`)
},[showRed,showBlue])

<button onClick ={()–≥ setShowRed(showRed => !showRed)}>Pika</button>
```

# 条件に合ったもの追記していく

```javascript
<div className={flg & 'colorRed'}>hoge!</div>
```

# 条件に合った場合、合わなかった場合を追記する

```javascript
<div className={flg ? 'colorRed' : 'colorBlue'}>hoge!</div>
```

# クラス名の一部を変更する

```javascript
type flg='blue' | 'red'
<div className={`color`${flg}}>
hoge!
</div>
```

#

# はじめに
