---
title: 'React で 動的にclassNameを適用する方法をまとめてみた。'
date: '2021-03-06'
tags: ['react', 'className']
draft: false
summary: 'Reactで動的に className　を適用する方法をまとめてみます。'
---

# React で動的に className を適用する方法をまとめてみます。

# 通常の className

通常のクラスを付与する方法は以下の通りです。

```
<div className="hoge">
  HOGE
</div>
```

クラスを複数付ける時も同じ。

```
<div className='hoge hage'>
  フランス
</div>
```

# 動的な className

## 条件に合ったもの追記していく

```javascript
<div className={flg & 'colorRed'}>hoge!</div>
```

## 条件に合った場合、合わなかった場合を追記する

```javascript
<div className={flg ? 'colorRed' : 'colorBlue'}>hoge!</div>
```

## クラス名の一部を変更する

```javascript
type flg='blue' | 'red'
<div className={`color`${flg}}>
hoge!
</div>
```
