---
id: network
title: Networking
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

多くのモバイルアプリでは、リモート URL からリソースを読み込む必要があります。REST API に POST リクエストを行いたい場合や、別のサーバーから静的コンテンツのチャンクを取得する必要がある場合があります。

## Using Fetch

React Native は、お客様のネットワークニーズに対応する [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) を提供します。以前に `XMLHttpRequest` や他のネットワーク API を使ったことがあるなら、Fetch は馴染みがあるでしょう。追加情報については、MDN の [Fetchを使う](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) に関するガイドを参照してください。

### Making requests

任意の URL からコンテンツを取得するためには、fetch に URL を渡して取得できます。

```tsx
fetch('https://mywebsite.com/mydata.json');
```

Fetch には、HTTP リクエストをカスタマイズできるオプションの 2 番目の引数も取ります。追加のヘッダーを指定したり、POST リクエストを行ったりすることもできます。

```tsx
fetch('https://mywebsite.com/endpoint/', {
  method: 'POST',
  headers: {
    Accept: 'application/json',
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    firstParam: 'yourValue',
    secondParam: 'yourOtherValue',
  }),
});
```

プロパティの全リストについては、[Fetch Request docs](https://developer.mozilla.org/en-US/docs/Web/API/Request) をご覧ください。

### Handling the response

上記の例は、リクエストを行う方法を示しています。多くの場合、レスポンスをどうにかしたいと思うでしょう。

ネットワークは本質的に非同期操作です。fetch メソッドは [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) を返すので、非同期で動作するコードを簡単に記述できます。

```tsx
const getMoviesFromApi = () => {
  return fetch('https://reactnative.dev/movies.json')
    .then(response => response.json())
    .then(json => {
      return json.movies;
    })
    .catch(error => {
      console.error(error);
    });
};
```

React Native アプリでは `async`/`await` シンタックスを使用することもできます。

```tsx
const getMoviesFromApiAsync = async () => {
  try {
    const response = await fetch(
      'https://reactnative.dev/movies.json',
    );
    const json = await response.json();
    return json.movies;
  } catch (error) {
    console.error(error);
  }
};
```

`fetch` でスローされるエラーをキャッチするのを忘れないようにしてください。さもないと、エラーは黙ってドロップされてしまいます。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Fetch%20Example&ext=js
import React, {useEffect, useState} from 'react';
import {ActivityIndicator, FlatList, Text, View} from 'react-native';

const App = () => {
  const [isLoading, setLoading] = useState(true);
  const [data, setData] = useState([]);

  const getMovies = async () => {
    try {
      const response = await fetch('https://reactnative.dev/movies.json');
      const json = await response.json();
      setData(json.movies);
    } catch (error) {
      console.error(error);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    getMovies();
  }, []);

  return (
    <View style={{flex: 1, padding: 24}}>
      {isLoading ? (
        <ActivityIndicator />
      ) : (
        <FlatList
          data={data}
          keyExtractor={({id}) => id}
          renderItem={({item}) => (
            <Text>
              {item.title}, {item.releaseYear}
            </Text>
          )}
        />
      )}
    </View>
  );
};

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Fetch%20Example&ext=tsx
import React, {useEffect, useState} from 'react';
import {ActivityIndicator, FlatList, Text, View} from 'react-native';

type Movie = {
  id: string;
  title: string;
  releaseYear: string;
};

const App = () => {
  const [isLoading, setLoading] = useState(true);
  const [data, setData] = useState<Movie[]>([]);

  const getMovies = async () => {
    try {
      const response = await fetch('https://reactnative.dev/movies.json');
      const json = await response.json();
      setData(json.movies);
    } catch (error) {
      console.error(error);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    getMovies();
  }, []);

  return (
    <View style={{flex: 1, padding: 24}}>
      {isLoading ? (
        <ActivityIndicator />
      ) : (
        <FlatList
          data={data}
          keyExtractor={({id}) => id}
          renderItem={({item}) => (
            <Text>
              {item.title}, {item.releaseYear}
            </Text>
          )}
        />
      )}
    </View>
  );
};

export default App;
```

</TabItem>
</Tabs>

> iOS 9.0 以降では、デフォルトで App Transport Secruity (ATS) が適用されます。ATS では、任意の HTTP 接続にHTTPS を要求します。クリアテキストの URL (`http` で始まるもの) からフェッチする必要がある場合は、まず [ATS 例外を追加](integration-with-existing-apps.md#test-your-integration) を行う必要があります。アクセスする必要のあるドメインが事前にわかっている場合は、それらのドメインにのみ例外を追加する方が安全です。実行時までドメインがわからない場合は、[ATSを完全に無効にする](app-store.md #1-enable-app-transport-security) ことができます。ただし、2017 年 1 月から [Apple の App Store レビューでは、ATS を無効にする合理的な理由が必要になる](https://forums.developer.apple.com/thread/48979) ことに注意してください。詳細については、[Apple のドキュメント](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW33) を参照してください。

> Android では、API レベル 28 以降、クリアテキストトラフィックもデフォルトでブロックされます。この動作は、アプリマニフェストファイルで [`android:usesCleartextTraffic`](https://developer.android.com/guide/topics/manifest/application-element#usesCleartextTraffic) を設定することで無効にできます。

## Using Other Networking Libraries

[XMLHttpRequest API](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) は React Native に組み込まれています。つまり、このAPIに依存する [frisbee](https://github.com/niftylettuce/frisbee) や [axios](https://github.com/axios/axios) などのサードパーティのライブラリを使用することも、必要に応じて XMLHttpRequest API を直接使用することもできます。

```tsx
const request = new XMLHttpRequest();
request.onreadystatechange = e => {
  if (request.readyState !== 4) {
    return;
  }

  if (request.status === 200) {
    console.log('success', request.responseText);
  } else {
    console.warn('error');
  }
};

request.open('GET', 'https://mywebsite.com/endpoint/');
request.send();
```

> ネイティブアプリには [CORS](http://en.wikipedia.org/wiki/Cross-origin_resource_sharing) の概念がないため、XMLHttpRequest のセキュリティモデルはウェブとは異なります。

## WebSocket Support

React Native は [WebSockets](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket) もサポートしています。これは、単一の TCP 接続で全二重通信チャネルを提供するプロトコルです。

```tsx
const ws = new WebSocket('ws://host.com/path');

ws.onopen = () => {
  // connection opened
  ws.send('something'); // send a message
};

ws.onmessage = e => {
  // a message was received
  console.log(e.data);
};

ws.onerror = e => {
  // an error occurred
  console.log(e.message);
};

ws.onclose = e => {
  // connection closed
  console.log(e.code, e.reason);
};
```

## Known Issues with `fetch` and cookie based authentication

以下のオプションは現在 `fetch`では動作しません

- `redirect:manual`
- `credentials:omit`

* Android で同じ名前のヘッダーを使用すると、最新のヘッダーのみが表示されます。一時的な解決策については、https://github.com/facebook/react-native/issues/18837#issuecomment-398779994 をご覧ください。
* Cookie ベースの認証は現在不安定です。提起された問題の一部はこちらでご覧いただけます:https://github.com/facebook/react-native/issues/23185
* iOS では、少なくとも、`302`を介してリダイレクトされたときに、`Set-Cookie`ヘッダーが存在する場合、クッキーは正しく設定されません。リダイレクトは手動で処理できないため、リダイレクトが期限切れのセッションの結果である場合、リクエストが無限大になるというシナリオが発生する可能性があります。

## Configuring NSURLSession on iOS

アプリケーションによっては、iOS 上で動作する React Native アプリケーションのネットワークリクエストに使用される基盤となる`NSURLSession`に、カスタム`NSURLSessionConfiguration`を提供するのが適切な場合があります。たとえば、アプリからのすべてのネットワークリクエストに対してカスタムのユーザーエージェント文字列を設定したり、`NSURLSession` に一時的な `NSURLSessionConfiguration` を指定したりする必要があるかもしれません。このようなカスタマイズは、`RCTSetCustomNSURLSessionConfigurationProvider` という関数を使って行うことができます。`rctSetCustomNSURLSessionConfigurationProvider` が呼び出されるファイルに、次のインポートを忘れずに追加してください。

```objectivec
#import <React/RCTHTTPRequestHandler.h>
```

`RCTSetCustomNSURLSessionConfigurationProvider` は、React が必要とするときにすぐに使用できるように、アプリケーションライフサイクルの早い段階で呼び出す必要があります。次に例を示します。

```objectivec
-(void)application:(__unused UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

  // set RCTSetCustomNSURLSessionConfigurationProvider
  RCTSetCustomNSURLSessionConfigurationProvider(^NSURLSessionConfiguration *{
     NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];
     // configure the session
     return configuration;
  });

  // set up React
  _bridge = [[RCTBridge alloc] initWithDelegate:self launchOptions:launchOptions];
}
```
