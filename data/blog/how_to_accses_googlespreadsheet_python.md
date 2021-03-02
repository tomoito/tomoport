---
title: 'YPython から Google Spread Sheets へのアクセス方法'
date: '2021-03-01'
tags: ['python', 'youtube']
draft: false
summary: 'Python から Google Spread Sheets へのアクセス方法'
---

# Python から Google Spread Sheets へのアクセス方法

# はじめに

この記事では、Python から Google Spread Sheets へのアクセス方法を解説します。

# 環境設定

VSCode で Python の実行環境を用意します。
以下の記事をご参照ください。
https://qiita.com/SolKul/items/f078877acd23bb1ea5b5

# Google Spread Sheets とは

Google スプレッドシートは、スプレッドシートを作成および編集するための無料の Web ベースのプログラムです。

Google スプレッドシートは、Google ドキュメントや Google スライドとともに、Google が Google ドライブと呼んでいるものの一部です。

Google スプレッドシートは、スプレッドシートの要件がそれほど大きくない場合、複数のデバイスからリモートで作業する場合、または他のユーザーと共同作業する場合に最適です。

# Google Spread Sheets に Python からアクセスするための下準備

## 新しいプロジェクトを作成する

こちらからプロジェクトを作成します。

https://console.developers.google.com/cloud-resource-manager

プロジェクトを作成をクリックします。

![YoutubeAPIa](../static/images/googlespread/0_7f08e4aa6f3240d905b5f7f26ee3001e.png)

プロジェクト名を適当に入力します。今回は「GoogleSpreadSheetsPython」としました。
![YoutubeAPIa](../static/images/googlespread/2_6b1452eda90be693204fcf299ca982e4.png)

しばらくすると、プロジェクトが完成します。

![YoutubeAPIa](../static/images/googlespread/4_6b1452eda90be693204fcf299ca982e4.png)

## Google Drive API を有効

Google drive と検索し、「Google Drive API」を選択します
![YoutubeAPIa](../static/images/googlespread/5_6b1452eda90be693204fcf299ca982e4.png)

有効にします。
![YoutubeAPIa](../static/images/googlespread/7_6b1452eda90be693204fcf299ca982e4.png)

## Google Spread Sheets API を有効にする

Google Drive API と同様に、Google Spread Sheet API も有効にします。
![YoutubeAPIa](../static/images/googlespread/8_6b1452eda90be693204fcf299ca982e4.png)

## 認証情報を設定する

サービスアカウントを選択します。
![YoutubeAPIa](../static/images/googlespread/10_6b1452eda90be693204fcf299ca982e4.png)

サービスアカウント名を記入して、作成してください。

![YoutubeAPIa](../static/images/googlespread/11_6b1452eda90be693204fcf299ca982e4.png)

権限にオーナーを追加してください。
![YoutubeAPIa](../static/images/googlespread/12_6b1452eda90be693204fcf299ca982e4.png)

## 秘密鍵を生成する

鍵を追加をクリックしまっす。

![YoutubeAPIa](../static/images/googlespread/13_6b1452eda90be693204fcf299ca982e4.png)

JSON タイプを指定

![YoutubeAPIa](../static/images/googlespread/14_6b1452eda90be693204fcf299ca982e4.png)

ローカルに Json ファイルが作成されます。
![YoutubeAPIa](../static/images/googlespread/15_6b1452eda90be693204fcf299ca982e4.png)

## Google Spread Sheet の共有設定をする

スプレッドシート共有
こちらからスプレッドシートを作成します。

https://docs.google.com/spreadsheets/create

シート名を入力します。今回は「test001」としました。

![YoutubeAPIa](../static/images/googlespread/16_6b1452eda90be693204fcf299ca982e4.png)

## Google Spread Sheets の key を取得

Google Spread Sheets のリンクから key を取得できます。

```
https://docs.google.com/spreadsheets/d/xxxxxxxxxxxxxxxxxx/edit#gid=0
```

xxxxxxxxxxxxxxxxxx が key になります。
2-3 で必要になるので、控えておきましょう。

![YoutubeAPIa](../static/images/googlespread/17_6b1452eda90be693204fcf299ca982e4.png)

## パッケージインストール

「gspread」と「oauth2client」をインストールします。

```python
$ pip install gspread
$ pip install oauth2client
```

# Google スプレッドシートを編集するプログラミングを作成します。

```python
import gspread
from oauth2client.service_account import ServiceAccountCredentials

#2つのAPIを記述しないとリフレッシュトークンを3600秒毎に発行し続けなければならない
scope = ['https://spreadsheets.google.com/feeds','https://www.googleapis.com/auth/drive']

#認証情報設定
#ダウンロードしたjsonファイル名をクレデンシャル変数に設定（秘密鍵、Pythonファイルから読み込みしやすい位置に置く）
credentials = ServiceAccountCredentials.from_json_keyfile_name('xxxxxxxxxxxxxxxxx.json', scope)

#OAuth2の資格情報を使用してGoogle APIにログインします。
gc = gspread.authorize(credentials)

#共有設定したスプレッドシートキーを変数[SPREADSHEET_KEY]に格納する。
SPREADSHEET_KEY = 'xxxxxxxxxxxxxxxxxxxx'

#共有設定したスプレッドシートのシート1を開く
worksheet = gc.open_by_key(SPREADSHEET_KEY).worksheet("シート1")


```

# プログラム実行

シートを開いて「Hello World!」と編集されていたら完成です。
https://docs.google.com/spreadsheets/

# コード解説

```python
#A!セルの値を受け取る
import_value = worksheet.acell('A1').value


#上と同じ
import_value = worksheet.cell(1,1).value
```

```python
#A1セルの値を１に更新する
ws.update_acell('A1',1)

```

```python
#A1セルの値に１００を加算した値をB1セルに表示させる


```

```python

```
