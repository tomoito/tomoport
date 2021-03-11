---
title: 'Twitter API v2 の仕様を理解する'
date: '2021-03-11'
tags: ['python', 'twitter']
draft: false
summary: 'Twitter API v2 を深掘りします'
---

Twitter API で取得できる情報は、ツイート情報だけに限らず、ユーザー、メディア、場所、投票が取得できます。
必要な値をフィールドに指定するだけなら簡単なのですが、そういうわけにもいきません。

TwitterAPI は、さまざまなリソースへのアクセスを提供します。

1. Tweet object
2. User object
3. Media object
4. Place objects
5. Poll object

Tweet オブジェクトには、id、text、created_at などの「ルートレベル」の長いリストがあります。
Tweet オブジェクトは、ユーザー、メディア、投票、場所など、いくつかの子オブジェクトの「親」オブジェクトでもあります。

User オブジェクトは子オブジェクトとしても検索でき、Tweet オブジェクトで展開できます。

## 何も指定せずに取得

まず、GET / tweets エンドポイントを使用して ID でこのツイートをリクエストします。

Request:

```
curl --request GET --url 'https://api.twitter.com/2/tweets?ids=xxxxxxxxxxxxxxxxxxxx' --header 'Authorization: Bearer $BEARER_TOKEN'
```

Response

```
{
    "data": [
        {
            "id": "1260294888811347969",
            "text": "Don’t miss the Tweets about your Tweet. \n\nNow on iOS, you can see Retweets with comments all in one place. https://t.co/oanjZfzC6y"
        }
    ]
}
```

最小限の情報が取得できました。
デフォルトの Tweet オブジェクトは、id フィールドと text フィールドのみに制限されています。

ここから必要な情報を追加していきましょう。

まずは、欲しいフィールドがどのオブジェクトモデルに属しているかを確認します。
tweets オブジェクトに含まれているのであれば、tweet.fields= に追記していきます。
Tweet オブジェクトに存在する author_id を追加してみましょう

Request:

```
curl --request GET --url 'https://api.twitter.com/2/tweets?ids=xxxxxxxxxxxxxxxxxxxx&tweet.fields=author_id' --header 'Authorization: Bearer $BEARER_TOKEN'
```

Response

```
{
    "data": [
        {
            "id": "xxxxxx",
            "text": "Hello-",
            "author_id": "hogehogehogehogehogeho"
        }
    ]
}
```

複数指定する場合、カンマ区切りで連結していきます。

```
tweet.fields=attachments,author_id,created_at
```

次に、Tweet オブジェクトに含まれていないユーザーに関連するフィールドをリクエストします。
これを行うには、author_id 　をキーとして使用して expansions パラメーターを使用し、これをリクエストに追加します。

データベースの外部結合をイメージすると分かりやすいかもしれません。
(例)Tweet.外部キー = User.主キー

expansions で拡張するキーは、fields に存在している必要があります。

Request:

```
curl --request GET --url 'https://api.twitter.com/2/tweets?ids=xxxxxxxxxxxxxxxxxxxx&tweet.fields=author_id&expansions=author_id&user.fields=description,profile_image_url' --header 'Authorization: Bearer $BEARER_TOKEN'
```

Response

```
{
    "data": [
        {
            "id": "xxxxxxxxxxxxxxxxxx",
            "text": "こんにちわ",
            "author_id": "hogehogehogehogehogeho"
        }
    ],
    "includes": {
    "users": [
        {
            "description":"こんにちは。"
            "id"':"xxxxxxxxxx"
            "username":"tomo"
            "profile_image_url":"https://xxxxxxxxxxxxxxxxx.jpg"
            "name"':"tomotun"

        }
    ]
    }
}
```

Includes プロパティに User 情報が追記されています。
