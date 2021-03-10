---
title: 'Python から Twitter API v2 を操作する'
date: '2021-03-10'
tags: ['python', 'twitter']
draft: false
summary: 'Python から Twitter API v2 を操作する'
---

# Python から Twitter API v2 を操作する

# はじめに

2020 年 08 月 13 日に Twitter API v2 がリリースされましたが、それ以降一度も Twitter 操作をしていませんでした。
いい機会なので、v2 を使って操作をしたいと思います。

# API を使うまでの準備

## ステップ 1:開発者アカウントを取得する

新しい TwitterAPI の使用を開始するには、承認された開発者アカウントが必要です。 まだお持ちでない場合は、お申し込みいただけます。 承認された開発者アカウントを取得したら、次の手順に進みます。

## ステップ 2：新しい開発者ポータルエクスペリエンスに登録する

次に、新しい開発者ポータルエクスペリエンスに登録してください。 これにより、プロジェクトにアクセスして、新しい TwitterAPI を使用してアクセスできるようになります。

## ステップ 3：プロジェクトを作成し、アプリを接続します

次に、開発者ポータルで、新しいプロジェクトを作成します。

名前を付け、適切なユースケースを選択し、プロジェクトの説明を提供します。 次に、新しいアプリを作成するか、既存のアプリを接続します。

すいません、ステップごとの説明が不足しておりますので後日補足します。

# Python からアクセスする

```python
pip install requests
```

# コード

```
import requests
import json
from datetime import datetime

#このトークンを直接配置するのはよくないです。（説明のためにのみ置いてます。）
BEARER_TOKEN = "xxxxxxxxxxxxxxxxxxx"

def search_twitter(query, tweet_fields,max_results, bearer_token = BEARER_TOKEN):

    res = []
    nextPageToken = 'start'
    counts = 0
    nextPage="start"

    while(nextPage is not None):
        headers = {"Authorization": "Bearer {}".format(bearer_token)}

        if nextPage =='start':

            url = "https://api.twitter.com/2/tweets/search/recent?query={}&{}&{}".format(
                query, tweet_fields,max_results
            )
        else:
            url = "https://api.twitter.com/2/tweets/search/recent?query={}&{}&{}&{}".format(
            query, tweet_fields,max_results,nextPage
            )

        response = requests.request("GET", url, headers=headers)

        res_json = response.json()
        nextPage = "next_token={}".format(res_json['meta']['next_token'])
        for i in res_json['data']:

            date_list = i['created_at'].split('T')
            year, month, date = date_list[0].split('-')
            hour, minute, sec = date_list[1].split(':')
            sec = sec[:2]

            dic = {'content':i['text'],
                   'date':datetime(int(year),int(month),int(date),int(hour),int(minute),int(sec)),
                   'Id':i['id'],
                   'auther':i['author_id']}

            res.append(dic)

    return res

#search term
query = "中日"
tweet_fields = "tweet.fields=text,author_id,created_at"
max_results="max_results=100"

json_response = search_twitter_nextPage(query=query, tweet_fields=tweet_fields, max_results=max_results, bearer_token=BEARER_TOKEN)

```

bearer_token [str]：ステップ 1 でコピーしたベアラートークン。

query [str]：これは、目的のツイートを照合するために使用される実際の文字列です。
ツイートをハッシュタグでフィルタリングしたり、リツイートを識別したり、特定の単語やフレーズを除外したり、
特定の言語のツイートのみを含めたりすることができます。 これらのクエリ文字列の記述方法の専用の説明は、ここにあります。

tweet_fields [str]：添付ファイル、author_id、text など、クエリで返すフィールド。たとえば、ツイートの author_id、text、公開日が必要な場合、tweet_fields 文字列は次のようになります。
