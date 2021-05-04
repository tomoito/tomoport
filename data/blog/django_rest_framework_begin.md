---
title: 'Django Rest FrameWork を学ぶ'
date: '2021-03-014'
tags: ['python', 'django']
draft: false
summary: 'WebAPI を作成するために、Django Rest FrameWork の使い方を説明します。'
---

## Django Rest Framework を学ぶ
Django は Python で Webアプリケーションを作成するためのフレームワークですが、
Django Rest Frame Work は Rest API を構築することに特化した Django パッケージです。

RestAPi構築するためには、単にデータの読み書きができれば良いというわけではなく、
認証、フィルタリング、ページネーションなどが必要になります。
Django Rest Framework にはバックエンド構築に必要な機能が揃っていることが強みです。


## バックエンドの処理に専念できる
Django のフォー  ムやテンプレートを使えばフロントエンドの開発はできるのですが、最近流行りのSPAを構築するにはモダンなJSが必要になります。
そのため、フロントエンドはモダンなJSに任せることが可能になります。


## Model
データベースの定義をしていきます。テーブルとカラムの定義をモデルのクラスと
そのクラス変数の定義に対応させることでレコードをオブジェクトモデルとして扱うことができます。

models.py に django.db.models.Modelを軽症したクラスを使います

クラス内部の Meta:クラスに、テーブルの名前、ソート順、インデックス情報を追加していきます。


モデルオブジェクトの使い方
<Object>.Object.all()
<>.Object.filter()
<>.Object.get()

<>.save()


## シリアライズ
シリアライザは、 データを保持しておくための 「⼊れ物」 です。  インスタンス作成時の引数 「data」 に⼊⼒データ、 「instance」 にデータベースから取  得したモデルオブジェクトを取り、 属性 「validated_data」 や 「data」 にアクセスして  データを取り 出します。
