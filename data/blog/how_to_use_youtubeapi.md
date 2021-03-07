---
title: 'エガちゃんねるの再生回数の動向を知りたい！（Python × Youtube Data API を学ぶ)'
date: '2021-02-28'
tags: ['python', 'youtube']
draft: false
summary: 'Python から Youtube Data API の使い方を説明します。また、特定のチャンネルの再生数を動向を確認します。'
---

# エガちゃんねるの再生回数の動向を知りたい！（Python × Youtube Data API を学ぶ)

# はじめに

この記事では、Python から Youtube Data API を使用してチャンネルのデータ収集方法を解説します。
今回は私が大好きなエガチャンネルを取り上げ、再生回数の動向を確認していきます。

# 経緯

第 3 回 好きな YouTuber ランキングでなんと！！江頭 2 時 50 分さんの「エガちゃんねる」が
1 位に輝きました。
えがちゃんは、過去に出演していた人気テレビ番組が軒並み打ち切りになったことで、ほとんど見かけることがなくなっていました。
しかし、Youtuber としての活動で再び露出が増えることになると共に、私のようなエガロス達による圧倒的な支持により、あっという間に人気者になりました。

そしてこの人気ランキング１位という結果に嬉しい気持ちになった反面、当時の勢いがなくなってきたのではと感じるようにもなりました。
そこで、エガちゃんねるの再生数の動向を調べてみます。

Python と Youtube Data API を使いながら、検索したいと思います。

# 環境設定

VSCode で Python の実行環境を用意します。
以下の記事をご参照ください。
https://qiita.com/SolKul/items/f078877acd23bb1ea5b5

# youtube のデータをどうやって取得するか？

YouTube チャンネルの個別データを手動で拾ってたら、いくら時間があってもたりません。
そこで、YouTube チャンネルのデータを取得できる YouTube Data API を活用します。
この API を利用することで、特定のチャンネルに紐づく動画 ID を全て抽出し、再生数や
コメントなどを取得することができます。控えめに言って超便利です。

# YouTube Data API の API Key の取得

API を利用するためには、Google のアカウントが必要になります。

API を使えるまでに少し面倒な手順を踏まないといけません。

## まずは Cloud Console にアクセスします。

https://console.cloud.google.com

## 次に、『プロジェクトを作成』を押してください。

![Youtube API](https://gyazo.com/c754d6c14c479ca2a0cb8af3dedb5c9e.png"サンプル")

## 新しいプロジェクトを押してください

![Youtube API](https://i.gyazo.com/1808875a171ed6a1ac3321076d27dae3.png 'サンプル')

このようにプロジェクト名の入力を行う必要があるので、任意のプロジェクト名を入力して作成ボタンを押してください。

![Youtube API](https://gyazo.com/97d877975be89774f54584eefb29bcdf.png"サンプル")

プロジェクトが作成できたら、YouTube Data API v3 を選択し、『認証情報を作成』ボタンをクリックします。

認証情報の入力については、

・使用する API 　 → 　 YouTube Data API v3

・API を呼び出す場所　 → 　ウェブブラウザ（JavaScript）

・アクセスするデータの種類　 → 　一般公開データ

これらを選択し、『必要な認証情報』ボタンをクリックすれば、API Key が発行されます。
これで YouTube Data API v3 の API Key 取得が完了です。

# YouTube Data API でチャンネル情報を取得してみる

早速 Python を利用して Youtube Data API を使っていきましょう。

まずは、特定チャンネルの Channel ID をキーにして、チャンネル情報取得するサンプルを書いてみます。

## 必要なパッケージをインストール

YouTube Data API を使用するためのパッケージをインストールします。

```python
$ pip install google-api-python-client
```

バージョンによって動作しない可能性もありますので、私が検証したバージョンを記載しておきます。
Python 3.7.3
google-api-python-client 1.9.3

## YouTube チャンネル情報を検索

まずは、任意の.py ファイルを作成してください。

以下は、YoutubeAPI を利用するための準備になります。
API_KEY は事前準備で取得した API_KEY を入力します。

```python
from apiclient.discovery import build

# API情報
API_KEY = 'YOUR_API_KEY'
YOUTUBE_API_SERVICE_NAME = 'youtube'
YOUTUBE_API_VERSION = 'v3'

youtube = build(
YOUTUBE_API_SERVICE_NAME,
YOUTUBE_API_VERSION,
developerKey=API_KEY
)
```

実際に Channel ID をキーに検索をかけていきます。

```python
channel_res = youtube.channels().list(
    part = 'snippet,statistics',
    id = CHANNEL_ID
    ).execute()

for search_res in channel_res.get("items", []):
    print(search_res["snippet"]["title"])
```

実行結果を見るとエガチャンネルのタイトルが取得できました。

```
エガちゃんねる EGA-CHANNEL
```

ちなみに、取得したデータの json 構造は以下の通りです。

```
{
  "kind": "youtube#video",
  "etag": etag,
  "id": string,
  "snippet": {
    "publishedAt": datetime,
    "channelId": string,
    "title": string,
    "description": string,
    "thumbnails": {
      (key): {
        "url": string,
        "width": unsigned integer,
        "height": unsigned integer
      }
    },
    "channelTitle": string,
    "tags": [
      string
    ],
    "categoryId": string
  },
  "contentDetails": {
    "duration": string,
    "dimension": string,
    "definition": string,
    "caption": string,
    "licensedContent": boolean,
    "regionRestriction": {
      "allowed": [
        string
      ],
      "blocked": [
        string
      ]
    },
    "contentRating": {
      "mpaaRating": string,
      "tvpgRating": string,
      "bbfcRating": string,
      "chvrsRating": string,
      "eirinRating": string,
      "cbfcRating": string,
      "fmocRating": string,
      "icaaRating": string,
      "acbRating": string,
      "oflcRating": string,
      "fskRating": string,
      "kmrbRating": string,
      "djctqRating": string,
      "russiaRating": string,
      "rtcRating": string,
      "ytRating": string
    }
  },
  "status": {
    "uploadStatus": string,
    "failureReason": string,
    "rejectionReason": string,
    "privacyStatus": string,
    "license": string,
    "embeddable": boolean,
    "publicStatsViewable": boolean
  },
  "statistics": {
    "viewCount": unsigned long,
    "likeCount": unsigned long,
    "dislikeCount": unsigned long,
    "favoriteCount": unsigned long,
    "commentCount": unsigned long
  },
  "player": {
    "embedHtml": string
  },
  "topicDetails": {
    "topicIds": [
      string
    ],
    "relevantTopicIds": [
      string
    ]
  },
  "recordingDetails": {
    "locationDescription": string,
    "location": {
      "latitude": double,
      "longitude": double,
      "altitude": double
    },
    "recordingDate": datetime
  },
  "fileDetails": {
    "fileName": string,
    "fileSize": unsigned long,
    "fileType": string,
    "container": string,
    "videoStreams": [
      {
        "widthPixels": unsigned integer,
        "heightPixels": unsigned integer,
        "frameRateFps": double,
        "aspectRatio": double,
        "codec": string,
        "bitrateBps": unsigned long,
        "rotation": string,
        "vendor": string
      }
    ],
    "audioStreams": [
      {
        "channelCount": unsigned integer,
        "codec": string,
        "bitrateBps": unsigned long,
        "vendor": string
      }
    ],
    "durationMs": unsigned long,
    "bitrateBps": unsigned long,
    "recordingLocation": {
      "latitude": double,
      "longitude": double,
      "altitude": double
    },
    "creationTime": string
  },
  "processingDetails": {
    "processingStatus": string,
    "processingProgress": {
      "partsTotal": unsigned long,
      "partsProcessed": unsigned long,
      "timeLeftMs": unsigned long
    },
    "processingFailureReason": string,
    "fileDetailsAvailability": string,
    "processingIssuesAvailability": string,
    "tagSuggestionsAvailability": string,
    "editorSuggestionsAvailability": string,
    "thumbnailsAvailability": string
  },
  "suggestions": {
    "processingErrors": [
      string
    ],
    "processingWarnings": [
      string
    ],
    "processingHints": [
      string
    ],
    "tagSuggestions": [
      {
        "tag": string,
        "categoryRestricts": [
          string
        ]
      }
    ],
    "editorSuggestions": [
      string
    ]
  }
}
```

# データ取得の流れ

本プログラムでは、以下の流れで Youtube チャンネル内の全ての投稿動画のデータを取得します。

1. Youtube チャンネル ID をキーに、プレイリストの ID を取得
2. 1.で取得したプレイリスト ID をキーに、動画タイトルと投稿日時、動画 ID を取得
3. 2.で取得した動画 ID から、動画の再生回数を取得

## プレイリスト ID の取得

```python
search_res = youtube.channels().list(
part= 'snippet,contentDetails',
id=id_,
).execute()
print(search_res['items'][0])
```

print 結果は以下の通りです。
![YoutubeAPIa](https://gyazo.com/02e437471c01dc3e208db379d9c8063d.png"サンプルa")

プレイリスト ID は ['contentDetails']['relatedplaylists']['uploads'] に格納されています。

# 動画 ID の取得

プレイリスト ID をキーとして、動画タイトル、投稿日時、動画 ID の取得になります。

```python
def GetPlaylistInfo(playlist_id, API_KEY):

    res = []
    nextPageToken = 'start'
    counts = 0

    while(nextPageToken is not None):

        youtube = build(API_SERVICE_NAME, API_VERSION, developerKey=API_KEY)

        if(nextPageToken == 'start'):
            search_res = youtube.playlistItems().list(
            part= 'snippet',
            playlistId=playlist_id,
            maxResults = 50,
            ).execute()
            nextPageToken = search_res['nextPageToken']
        else:
            search_res = youtube.playlistItems().list(
            part= 'snippet',
            playlistId=playlist_id,
            maxResults = 50,
            pageToken = nextPageToken
            ).execute()
            try:
                nextPageToken = search_res['nextPageToken']
            except:
                nextPageToken = None

    return res
```

一度に取得できる動画数は 50 までとなっています。
それ以上の動画数を取得するためには、nextPageToken に次のページが取得されていますので、
再起的に pageToken を入れ替えることで 50 個以上のデータが取得できます。

投稿時間は、ここで str から datetime 型に変換しておきます。

# 動画の詳細情報の取得

動画 ID から、動画の詳細情報(再生数、評価数、コメント数、再生時間)を取得します。
引数 id には動画 ID を与えます。

```python

def GetVideoInfo(video_id, API_KEY):

    youtube = build(API_SERVICE_NAME, API_VERSION, developerKey=API_KEY)

    search_res = youtube.videos().list(
    part= 'statistics,contentDetails',
    id=video_id,
    ).execute()

    video_info = search_res['items'][0]
    details = {'viewCount':int(video_info['statistics']['viewCount']),
               'likeCount':int(video_info['statistics']['likeCount']),
               'dislikeCount':int(video_info['statistics']['dislikeCount']),
               'commentCount':int(video_info['statistics']['commentCount']),
               'duration':ConvertDuration(str(video_info['contentDetails']['duration']))}

    return details

def ConvertDuration(string):
    string = string.replace('PT', '')
    strings = re.split('\D',string)[:-1]
    if(len(strings) == 3):
        delta = datetime.timedelta(hours=int(strings[0]),
                                   minutes=int(strings[1]),
                                   seconds=int(strings[2]))
    elif(len(strings) == 2):
        delta = datetime.timedelta(minutes=int(strings[0]),
                                   seconds=int(strings[1]))
    elif(len(strings) == 1):
        delta =datetime.timedelta(seconds=int(strings[0]))
    else:
        delta = datetime.timedelta(seconds=0)

    return delta.seconds
```

二つ目の ConvertDuration は、動画再生時間を datetime.timedelta で変換する関数です。
以下のように、すべての動画の情報をリストに辞書型で格納し、numpy 形式に変換してから.npy 形式で保存します。

```python
for n,d in enumerate(tqdm(np.array([i['Id'] for i in dic_total]))):
    details = YoutubeVideoDetails(d,API_KEY)
    dic_total[n].update(details)
dic_total = np.array(dic_total)

np.save('ChannelTitle-data.npy',dic_total) #.npy形式で保存
```

# 取得したデータの解析例

まとめたデータからは以下のように各情報を取り出します。

```python
dates = np.array([i['date'] for i in dic_total]) # 投稿日時
viewCount = np.array([i['viewCount'] for i in dic_total]) # 再生回数
```

# 再生数推移

普通に再生数推移を表示するとめちゃくちゃになるので、移動平均も一緒にプロットすることをお勧めします。
下の図は、散布図が各動画の再生回数、実戦が 7 日単位での移動平均です。

```python
fig = plt.figure(figsize=(7,4),dpi=200)
# 散布図表示
plt.scatter(dates,viewCount,s=20,alpha=0.5)
# 移動平均をプロット
plt.plot(dates,np.convolve(viewCount,np.ones(7)/7.0,mode='same'),lw=2,c='b')
# 日時ラベルを30度傾斜
labels = plt.gca().get_xticklabels()
plt.setp(labels,rotation=30)
plt.title('総再生数推移',fontsize=14)
plt.show()

```

![YoutubeAPIa](https://gyazo.com/1bb4e1b78fc9017f499778a073a9be58.png"サンプルa")

# 再生数ランキング

```
ranking = dic_total[np.argsort(viewCount)[::-1]]
print(ranking[:5]) # 上位5タイトルを表示
```

結果は以下のようになります。
これは、Youtube チャンネル上で動画を人気順に並べ替えた結果と同じになります。

```python
[{'title': 'カメラの前で初めて歌う！THE BLUE HEARTS「人にやさしく」', 'date': datetime.datetime(2020, 2, 7, 13, 21, 7), 'Id': 'lHIYBDbQmj8', 'viewCount': 7196085, 'likeCount': 241175, 'dislikeCount': 2175, 'commentCount': 25684, 'duration': 606}
 {'title': '江頭2:50、YouTubeに参上！【BADASS SAMURAI】', 'date': datetime.datetime(2020, 1, 31, 18, 9, 19), 'Id': '8X1h68C0eoA', 'viewCount': 4551352, 'likeCount': 234160, 'dislikeCount': 2724, 'commentCount': 19644, 'duration': 701}
 {'title': 'ネットの江頭伝説は本当なのか？（名言編）', 'date': datetime.datetime(2020, 2, 3, 15, 29, 2), 'Id': 'K6PSUSvcRto', 'viewCount': 4321973, 'likeCount': 170432, 'dislikeCount': 1203, 'commentCount': 14910, 'duration': 698}
 {'title': '【エガドライブ】江頭の愛車でBBQ行こうぜ！', 'date': datetime.datetime(2020, 7, 19, 8, 52, 1), 'Id': 's6l6HnGMLj8', 'viewCount': 4206433, 'likeCount': 50780, 'dislikeCount': 871, 'commentCount': 3134, 'duration': 1011}
 {'title': '江頭、大好きな人とデートします。', 'date': datetime.datetime(2020, 5, 31, 7, 35, 9), 'Id': 'xcoH_2r8Sw0', 'viewCount': 4072725, 'likeCount': 45312, 'dislikeCount': 1051, 'commentCount': 2673, 'duration': 1049}
 {'title': '150万人突破記念！視聴者の質問にガチで答える', 'date': datetime.datetime(2020, 2, 23, 8, 49, 32), 'Id': '5zy_Vk2ty04', 'viewCount': 3557717, 'likeCount': 105797, 'dislikeCount': 1205, 'commentCount': 7255, 'duration': 865}
 {'title': '宮迫に一言物申す！', 'date': datetime.datetime(2020, 11, 22, 6, 16, 48), 'Id': '5FVqPgxCspY', 'viewCount': 3496171, 'likeCount': 110314, 'dislikeCount': 3864, 'commentCount': 8595, 'duration': 1118}
 {'title': '100万人達成でありがとうの股間大爆破！', 'date': datetime.datetime(2020, 2, 9, 16, 23, 20), 'Id': '9_fHqqE4qss', 'viewCount': 3429433, 'likeCount': 156087, 'dislikeCount': 1071, 'commentCount': 14166, 'duration': 521}
 {'title': '【江頭×宮迫】日本一高いバンジー、２人分予約してた', 'date': datetime.datetime(2020, 11, 24, 5, 2, 36), 'Id': 'hVL1ZFRxou8', 'viewCount': 3428986, 'likeCount': 85720, 'dislikeCount': 2636, 'commentCount': 7208, 'duration': 1475}
 {'title': '【エガ散歩】エガちゃんVS原宿の若者 EGA-WALK in Harajuku', 'date': datetime.datetime(2020, 2, 11, 3, 41, 24), 'Id': '1d0Ry5NMhPg', 'viewCount': 3290129, 'likeCount': 117499, 'dislikeCount': 1087, 'commentCount': 9479, 'duration': 852}]
```
