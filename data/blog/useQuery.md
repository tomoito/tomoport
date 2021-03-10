---
title: 'react-queryを使ってみる'
date: '2021-03-08'
tags: ['React', 'react-query']
draft: false
summary: 'react-queryを使ってみます。'
---

# react-query を使ってみる。

外部 API データをフェッチする時に、毎回以下のようなコードを書きます。

```javascript
import React, { useState, useEffect } from 'react'
import axios from 'axios'
function App() {
  const [isLoading, setLoading] = useState(false)
  const [isError, setError] = useState(false)
  const [data, setData] = useState({})

  useEffect(() => {
    const fetchData = async () => {
      setError(false)
      setLoading(true)

      try {
        const response = await axios('https://jsonplaceholder.typicode.com/posts/1')

        setData(response.data)
      } catch (error) {
        setError(true)
      }
      setLoading(false)
    }
    fetchData()
  }, [])
  return (
    <div className="App">
      {isError && <div>エラー</div>}

      {isLoading ? <div>Loading ...</div> : <pre>{JSON.stringify(data)}</pre>}
    </div>
  )
}
export default App
```

上記のコードでは、useState フックと useEffect フックの両方が必要であり、3 つの異なる状態(data,isLoading,isError)を使用してデータを保存し、
アプリケーションがデータをフェッチしているか、API からのエラー応答がすでにあるかを判断します。
このパターンは、ほとんどの外部データからデータをフェッチするロジックで何度も繰り返されます。。。。

何か解決策はないでしょうか・・・

# 解決策!

React Query は上記の処理を全部やってくれます。
React Query では、さらに以下の処理を行ってくれます。

\*自動キャッシュ

\*自動再取得

\*エラーハンドリング

早速使ってみましょう。

# React Query を使ってみる

```javascript
async function fetchData(itemId) {
  const res = await fetch(`https://example.com/api/${itemId}`)
  const json = await res.json()
  return json
}
function Component({ itemId }) {
  const { isLoading, data } = useQuery(['fetchData', itemId], fetchData)
  if (isLoading) {
    return <p>Loading ...</p>
  } else {
    return <DoStuffWithData data={data} />
  }
}
```

レンダリングの処理をかなり簡潔に書くことができました。

useQuery()の使い方は、第一引数にクエリキー、第二引数にデータ解決する非同期関数になります。

クエリーキーに基づいて、クエリー結果をキャッシュします。つまり、一意な値でないといけません。

今回のサンプルでは、配列で渡していますが、シンプルな文字列からオブジェクトでも可能です。クエリーキーで渡した値は、第二引数の非同期関数の引数として受け取れます。

useQuery の戻り値に注目してみましょう。
実際のデータに加えて、isLoading が取得できます。これによって、
データのフェッチ中かどうかが判断できます。

…何か既視感がありますね。。。
この useQuery()の部分が、カスタム Hooks（useEffect()で loading の state を管理）で作成していました。
