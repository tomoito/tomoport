---
title: 'react-google-mapsを使った地図アプリでマーカーを表示する'
date: '2021-03-03'
tags: ['react', 'googlemap']
draft: false
summary: 'reactからreact-google-mapsを使って、地図のレンダリングとマーカーの配置の作り方を説明します。'
---

# react-google-maps を使った地図アプリでマーカー表示する

React アプリで Google マップをレンダリングする機会があったので紹介します。
Google Maps API の React ライブラリはいくつか種類がありますが、スター数や Isssue を見て@react-google-maps/api を使用しました。

この react-google-maps / api を使用すると、マップ機能を簡単にレンダリングして実装できます。

この記事では以下のことを説明します。

1. Google マップをレンダリングする方法
2. マーカーを配置する方法
3. マーカーをクリックすると、メッセージボックス（infoWindows）表示させる方法

最終的なイメージは以下の感じです。

![hoge](../static/images/googlemap/1_f7e5b0ad44a53610e3d5cc23b08d30ec.gif)

# Google Map API を使用するための準備

Google Map API を使用する前に、Google Map アカウントを作成し、API キーを生成する必要があります。Google マップをコンポーネントでレンダリングするには、このキーが必要になります。ただ、API キーを設定をしなくても(一応)使えます。
まだの場合は下記を参考に作成してください。

https://www.zenrin-datacom.net/business/gmapsapi/api_key/

# Step 1 — React プロジェクトファイルを作成する

下記コマンドを実行し React のプロジェクトフォルダを新規作成します。

```javascript
% npx create-react-app react-google-maps-sample --typescript
% cd react-google-maps-api-sample
```

react-google-maps パッケージをインストールします。

```javascript
% npm @react-google-maps/api
```

# Step 2 — React コンポーネントでの Google マップのレンダリング

src フォルダーに MyComponent.jsx というコンポーネントを作成し、このコンポーネントを App.jsx に挿入して、マップを表示できるようにします。

MyComponent を編集していきます。
react-google-maps から GoogleMap および Loadscript コンポーネントをインポートします。
次にコードを GoogleMap コンポーネントを挿入します。

src/App.tsx

```javascript
const App = () => {
  return <MyComponent />
}
```

src/MyComponent.tsx

```javascript
const containerStyle = {
  height: '100vh',
  width: '100%',
}

const center = {
  lat: 35.69575,
  lng: 139.77521,
}

const MyComponent: React.FC = () => {
  return (
    <LoadScript googleMapsApiKey="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx">
      <GoogleMap mapContainerStyle={containerStyle} center={center} zoom={17}></GoogleMap>
    </LoadScript>
  )
}

export default MyComponent
```

警告：API キーは、意図しない目的で他のユーザーが使用できるため、パブリックリポジトリ（GitHub など）にコミットするファイルには保存しないでください。

この状態で、 npm run start を実行すると、GoogleMap の center プロパティに
指定した場所が起点となり、Google map が表示されます。

さらに改造していきます。

# Step3 - Google マップにクリック可能なマーカーを追加する

次に、指定の場所に赤いマーカーを配置します。やり方は簡単で、緯度座標と経度座標を含む場所の配列をマッピングし、マーカーコンポーネントをレンダリングします。

Marker を使うことによって、指定した位置にマーカーを配置できます。

```javascript
const sample = {
  lat: 35.69575,
  lng: 139.77521,
}

const containerStyle = {
  height: '100vh',
  width: '100%',
}

import { GoogleMap, LoadScript, Marker } from '@react-google-maps/api'

const MyComponent: React = () => {
  return (
    <LoadScript googleMapsApiKey="xxxxxxxxxxxxxxxxxxxxxxxxxxxx">
      <GoogleMap mapContainerStyle={containerStyle} center={center} zoom={17}>
        <Marker
          position={{
            lat: sample.lat,
            lng: sample.lng,
          }}
        ></Marker>
      </GoogleMap>
    </LoadScript>
  )
}

export default MyComponent
```

# Step4 - 地図のクリックイベントと infoWindow の追加

Maker コンポーネントに onClick イベントを追記します。
クリックの状態は useState にて管理をします。クリックされたら、infoWindow を表示するようにします。

```javascript
const LocationConst = [
  {
    info: '(^^)/',
    location: {
      lat: 35.69575,
      lng: 139.77521,
    },
  },
  {
    info: 'hoge',
    location: {
      lat: 35.168132,
      lng: 136.881663,
    },
  },
]

const MyComponent: React.FC = () => {
  const [selected, setSelected] = useState < any > null

  return (
    <LoadScript googleMapsApiKey="xxxxxxxxxxxxxxxxxxxxx">
      <GoogleMap mapContainerStyle={containerStyle} center={center} zoom={17}>
        {locationInfo.map((marker) => (
          <Marker
            key={`${marker.location.lat * marker.location.lng}`}
            position={{
              lat: marker.location.lat,
              lng: marker.location.lng,
            }}
            onClick={() => {
              setSelected(marker)
              // <InfoWindow>が描画されます。
            }}
          ></Marker>
        ))}

        {selected ? (
          // MarkerにマウスクリックされたときにInfoWindowが表示されます。
          <InfoWindow
            position={{
              lat: selected.location.lat,
              lng: selected.location.lng,
            }}
            onCloseClick={() => {
              setSelected(null)
            }}
          >
            <div>{selected.info}</div>
          </InfoWindow>
        ) : null}
      </GoogleMap>
    </LoadScript>
  )
}

export default MyComponent
```

# あとは好みで修正

私は位置情報をプロパティに渡すようにリファクタリングしています。

```javascript
<MyComponent center={center} locationInfo={LocationConst} />
```

github に公開していますので参考にして下さい。
