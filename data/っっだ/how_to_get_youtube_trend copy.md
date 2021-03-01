---
title: 'Youtube の急上昇ランキングを取得する方法（Python × Youtube Data API を学ぶ)'
date: '2021-03-01'
tags: ['markdown', 'code', 'features']
draft: false
summary: 'python から Youtube Data API を使って急上昇ランキングを取得する方法を説明します。'
---

# Youtube の急上昇ランキングを取得する方法（Python × Youtube Data API を学ぶ)

# はじめに

この記事では、Python から Youtube Data API (https://developers.google.cn/youtube/v3/getting-started?hl=ja)を使用してデータ収集方法を解説します。

# 経緯

友人から Youtube の急上昇を取得する方法はないかと聞かれて、プログラムを作成しました。

今日はその方法を説明します。

# 環境設定

VSCode で Python の実行環境を用意します。
以下の記事をご参照ください。
https://qiita.com/SolKul/items/f078877acd23bb1ea5b5

# データをどうやって取得するか？

前のブログでも解説しましたが、Youtube Data API を使いますので、
YouTube Data API v3 の API Key を取得してください。
前ブログを参照ください。
https://tomoport.vercel.app/blog/YoutubeAPI%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%A6%E3%83%81%E3%83%A3%E3%83%B3%E3%83%8D%E3%83%AB%E3%81%AE%E5%88%86%E6%9E%90%E3%81%99%E3%82%8B%E6%96%B9%E6%B3%95

## 必要なパッケージをインストール

Requests ライブラリをインストールします。

pip install requests
$ pip install requests

## 実際に書いてみよう

たいしたコード量ではないので、全て紹介します。

```python
# API_KEY="AIzaSyArPgqw1ivEjMrGj6vXCadIT1vVz7PeUZQ"
from apiclient.discovery import build
import requests

API_SERVICE_NAME = "youtube"
API_VERSION = "v3"
API_KEY = "AIzaSyArPgqw1ivEjMrGj6vXCadIT1vVz7PeUZQ"

parms = {
            'part': 'id,snippet',
            'maxResults': 10,
            'chart': 'mostPopular',
            'regionCode': 'JP',
            'key': API_KEY,

}
YOUTUBE_SEARCH_URL = 'https://www.googleapis.com/youtube/v3/videos'
r = requests.get(YOUTUBE_SEARCH_URL, params=parms)
results = r.json()['items']
dic_total = []

for result in results:

    video_data = {
        'title' : result['snippet']['title'],
        'id' : result['id'],
        'url' : f'https://www.youtube.com/watch?v={ result["id"] }',
        'channelTitle' : result['snippet']['channelTitle'],
        # 'description' : result['snippet']['description'],
        # 'thumbnail' : result['snippet']['thumbnails']['high']['url']
    }
    dic_total.append(video_data)

print(dic_total)

```

ポイントは、API リクエストのパラメータの chart パラメータには、mostPopular を指定します。

そうすることで、指定したコンテンツ地域および動画のカテゴリに関して最も人気のある動画を返します。

https://developers.google.com/youtube/v3/docs/videos/list?hl=ja

結果は以下のようになります

```
[{'title': '"This is ARASHI LIVE 2020.12.31" Digest Movie', 'id': 'bGqdS1lBq-w', 'url': 'https://www.youtube.com/watch?v=bGqdS1lBq-w', 'channelTitle': 'ARASHI'}, {'title': '"ARAFES 2020 at NATIONAL STADIUM" Digest Movie', 'id': '-BZEJodQ2Bs', 'url': 'https://www.youtube.com/watch?v=-BZEJodQ2Bs', 'channelTitle': 'ARASHI'}, {'title': '那須川天心vs志朗は驚愕「天心の完全試合」青木真也「彼が創るものを見続けたい」全試合期間限定無料公開中！', 'id': 'YFeoKJT5Q6Q', 'url': 'https://www.youtube.com/watch?v=YFeoKJT5Q6Q', 'channelTitle': 'ABEMA 格闘CH【公式】'}, {'title': 'ヒカル兄さんにねおチャンネルの今後を相談してみた！【緊急家族会議】', 'id': 'alQBuIZCGR0', 'url': 'https://www.youtube.com/watch?v=alQBuIZCGR0', 'channelTitle': 'ねおチャンネル'}, {'title': '１分間喧嘩ファイトで本気の殴り合いしてきた', 'id': 'DFmWG6Og4AA', 'url': 'https://www.youtube.com/watch?v=DFmWG6Og4AA', 'channelTitle': "JIN'S LIFE"}, {'title': '【宮迫×中田】ヒカル（前編）〜カリスマYouTuber成功の秘密〜【Win Win Wiiin】',
'id': '8rhMnFocaVw', 'url': 'https://www.youtube.com/watch?v=8rhMnFocaVw', 'channelTitle': '中田敦彦のYouTube大学 - NAKATA UNIVERSITY'}, {'title': '【コムドット】道あけろってどうゆう意味だったのかな？', 'id': 'uuadQn1I7UQ', 'url': 'https://www.youtube.com/watch?v=uuadQn1I7UQ', 'channelTitle': 'スカイピースの平和島'}, {'title': '乃木坂46&東京03｜乃木坂毎月劇場\u3000第11話「引っ越す」｜サッポロ一番\u3000カップスター\u3000和ラー', 'id': 'A0cxkujsfgA', 'url': 'https://www.youtube.com/watch?v=A0cxkujsfgA', 'channelTitle': 'サッポロ一番 公式'}, {'title': '1分最強の男を決める大会第0回大会', 'id': 'RqEg7fc48pQ', 'url': 'https://www.youtube.com/watch?v=RqEg7fc48pQ', 'channelTitle': '朝倉未来 Mikuru Asakura'}]

```
