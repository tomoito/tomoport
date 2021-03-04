---
title: 'Reactで Trello のような Drag&Drop (DnD)を最速で作成する方法'
date: '2021-03-04'
tags: ['react', 'react-dnd', 'react-trello']
draft: false
summary: 'reactからreact-google-mapsを使って、地図のレンダリングとマーカーの配置の作り方を説明します。'
---

# React で Trello のような Drag&Drop (DnD)を最速で作成する方法

# はじめに

今回は react-trello を利用して、最速で Drag&Drop を用いたリストの並び替えを実現したいと思います。

以下の DnD は 10 行くらいで作成ができます。あらかんたん。

![hoge](../static/images/react-trello/5db860b4d3ca6a127cd0a2a2bb2ddf01.gif)

# 動機

自前で Drag&Drop (DnD) を実装するのは大変だと思います。

DnD を実装するために、以下のライブラリを選択すると思います。

1. react-beautiful-dnd
2. react-dnd
3. react-draggable

stars、issues、updated を見ると react-beautiful-dnd
が一番アクティブに使われてる感じがします。

react-beautiful-dnd は以前から使っていましたが、
もっと簡単に実現できないかと考えていました。

あまりカスタマイズは必要がなく、シンプルに Dnd を実現するだけにうってつけのライブラリを見つけました。react-trello です

できることは単純ですが、単純な DnD アプリであれば、
これを使えるかもと思ったので紹介します。

## setup

create-react-app を用いて雛形を作成します。

```
npx create-react-app react-trello-sample
cd react-trello-sample
```

## 必要なライブラリをインストール

```
npm install --save react-trello
```

App.js を変更する

```
import React from "react";

import data from "./data.json";
import "./index.css";
import Board from "react-trello";

function App() {
  return (
    <div className="App">
      <h1>react-trello demo</h1>
      <Board data={data} draggable />
    </div>
  );
}


export default App;

```

data を Board コンポーネントに渡すだけで完了です。
サンプルデータ json をここに示します。

```
{
    "lanes": [
      {
        "id": "id1",
        "title": "plan",
        "label": "(^^)/",
        "style": {
          "width": 280
        },
        "cards": [
          {
            "id": "id1_card1",
            "title": "寝る",
            "label": "10時間",
            "description": "寝ない事にははじまらない"
          },
          {
            "id": "Plan2",
            "title": "食べる",
            "label": "1時間",
            "description": "好きなものを好きなだけ食べる"
          },
          {
            "id": "Plan3",
            "title": "ネットサーフィン",
            "label": "2時間",
            "description": "はろ～Youtube～"
          }
        ]
      },
      {
        "id": "Do",
        "title": "Do",
        "label": "(*´з`)",
        "style": {
          "width": 280
        },
        "cards": [
          {
            "id": "Wip1",
            "title": "ブログ",
            "label": "30 mins",
            "description": "毎日ブログ更新します"
          }
        ]
      },
      {
        "id": "BLOCKED",
        "title": "Action",
        "label": "(^^♪",
        "style": {
          "width": 280
        },
        "cards": []
      },
      {
        "id": "REPEAT",
        "title": "Check",
        "style": {
          "width": 280
        },
        "label": "(-_-)",
        "cards": [
          {
            "id": "Repeat1",
            "title": "お風呂",
            "label": "30 mins",
            "description": "良い湯だな～♪"
          }
        ]
      }
    ]
  }

```

# はい、出来上がりです。
