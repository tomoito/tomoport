---
title: 'ドラクエウォークで学ぶ、TypeScript入門'
date: '2021-03-05'
tags: ['TypeScript']
draft: false
summary: 'TypeScriptの型の作り方を楽しく学んできます。'
---

# ドラクエウォークで学ぶ、TypeScript 入門

# TypeScript とは

TypeScript は JavaScript に型を追加して使いやすくなった言語です。
ブラウザ上では直接実行できないので、JavaScript にトランスパイルされます。
簡単な例を提示します。

TypeScript

```
let hex: number = 100;
let color: string = "blue";
let flg: boolean = false;
```

JavaScript

```
let hex = 100;
let color = "blue";
let flg = false;
```

typeScript⇨javascript とは、単純にコンパイルされると型情報が消去されるだけです。

しかし、開発中に型があることによって、プロパティが存在しているかのチェックができます。コードがサジェストされることはとても爽快です。。

# TypeScript じゃないのだめなの？

キーがサジェストされることによって、開発中のミスが減りバグが減ることが期待されます。
また、型が用意されていることによって開発効率が上がり、コーディングが捗ります。
私は TypeScript を推しています。

# ドラクエウォークのこころを TypeScript で表現してみる。

## こころの情報は下記の json ファイルから取得することを想定します。

```javascript
{
        "name": "ゾーマ",
        "cost": 1,
        "color": "紫",
        "status": {
            "HP": 100,
            "MP": 50,
            "攻撃力": 6,
            "守備力": 20,
            "攻撃魔法": 120,
            "回復魔力": 9,
            "すばやさ": 90,
            "きようさ": 30
        },
        "option": {
            "呪文アップ": 10,
            "ヒャド耐性アップ": 10
        }
}
```

## ベースとなるココロの型を作っておきます。（json から型を取る）

TypeScript で深い Json 構造を１から作るのは面倒です。
そこで Json 構造から要素を取り出すときに型をとります。

```javascript
type TypeKokoro = typeof Data
```

こうすることで、どれだけ深くネストされたデータでも型を取得することができます。
推論された型を見ると、大体問題ありません。
これで終わりか？と言いたいところですが、もちろんこれから修正していきます。

color を見ると、string となっていますが、color は指定の５種類から選びたいです。
また、option に関しては、存在するプロパティとしないプロパティがありますので、
オプショナルにしたいです。
課題は以下の二点に鳴ります。

1.  color は'赤'、'青'、'紫'、'黄'、'緑'から選ぶ。
2.  option はプロパティが存在するものだけ記載されている。

## color の種類について

Uniton Type を使って実装していきましょう。

```javascript
type colorKind = '赤' | '青' | '紫' | '黄' | '緑'
```

これで指定の色のユニオンタイプができました。

## option はプロパティが存在するものだけ記載する

option オブジェクトのプロパティをオプショナルにします。
オプショナルにするには、Partial を使います。

（例）Partial の使い方

```javascript
type Person {
  firstName: string;
  lastName: string;
}

const Nobita: Partial<Person> = {
  firstName: "Nobi",
};
```

option プロパティをオプショナルにして、新しい型を作りたいと思います。

まず、Mapped Types によって、既存 Type の Key 情報を再利用して、あたらしい Type を定義しています。
さらに、Conditional Type を使って、与えられた型によって分岐した結果の型を返すことができます。
今回は型が option なら optional に変更し、color であれば先ほど定義した colorType に上書きします。

コードは以下のようになります。

```
type typeKokoroBefore<T, type1, type2, type3> = {
  [K in keyof T]:(K extends type1 ?
    Partial<{
    [X in keyof T[K]]?:number
  }>
    :K extends type2 ?
      type3
    :T[K]
    )
}
```

これで、ドラクエウォークのこころを表現できました。

```javascript
type kokoro = typeKokoroBefore<TypeDoraSolo, 'option', 'color', colorKind>
```
