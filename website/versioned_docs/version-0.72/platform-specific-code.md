---
id: platform-specific-code
title: Platform-Specific Code
---

クロスプラットフォームアプリを構築するときは、できるだけ多くのコードを再利用したいと思うでしょう。たとえば、Android と iOS で別々のビジュアルコンポーネントを実装したいなど、コードを異なったものにすることに意味があるシナリオが起こりえます。

React Native には、コードを整理してプラットフォームごとに分ける方法が2つあります。

- Using the [`Platform` module](platform-specific-code.md#platform-module).
- Using [platform-specific file extensions](platform-specific-code.md#platform-specific-extensions).

特定のコンポーネントには、1つのプラットフォームでのみ機能するプロパティがある場合があります。これらのpropsにはすべて `@platform` というアノテーションが付いており、ウェブサイトでは横に小さなバッジが付いています。

## Platform module

React Native は、アプリが実行されているプラットフォームを検出するモジュールを提供します。検出ロジックを使用して、プラットフォーム固有のコードを実装できます。コンポーネントのごく一部だけがプラットフォーム固有の場合は、このオプションを使用してください。

```tsx
import {Platform, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  height: Platform.OS === 'ios' ? 200 : 100,
});
```

`Platform.OS`は、iOSで実行しているときは`ios`、Androidで実行しているときは`android`になります。

また、`Platform.select`メソッドもあります。これは、キーが`'ios' | 'android' | 'native' | 'default'`のいずれかであるオブジェクトを指定すると、現在実行しているプラットフォームに最も適した値を返します。つまり、携帯電話で実行している場合は、`ios`キーと`android`キーが優先されます。それらが指定されていない場合は、`native`キーが使用され、次に`default`キーが使用されます。

```tsx
import {Platform, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    ...Platform.select({
      ios: {
        backgroundColor: 'red',
      },
      android: {
        backgroundColor: 'green',
      },
      default: {
        // other platforms, web for example
        backgroundColor: 'blue',
      },
    }),
  },
});
```

これにより、コンテナはすべてのプラットフォームで `flex: 1`、iOSでは赤の背景色、Androidでは緑の背景色、他のプラットフォームでは青の背景色になります。

`any`の値を受け入れるので、以下のようにプラットフォーム固有のコンポーネントを返すのにも使えます。

```tsx
const Component = Platform.select({
  ios: () => require('ComponentIOS'),
  android: () => require('ComponentAndroid'),
})();

<Component />;
```

```tsx
const Component = Platform.select({
  native: () => require('ComponentForNative'),
  default: () => require('ComponentForWeb'),
})();

<Component />;
```

### Detecting the Android version

Androidでは、`Platform`モジュールを使用して、アプリが実行されているAndroidプラットフォームのバージョンを検出することもできます。

```tsx
import {Platform} from 'react-native';

if (Platform.Version === 25) {
  console.log('Running on Nougat!');
}
```

**Note**:`Version`はAndroid OSのバージョンではなく、Android APIのバージョンに設定されています。マッピングを見つけるには、[Android Version History](https://en.wikipedia.org/wiki/Android_version_history#Overview) を参照してください。

### Detecting the iOS version

iOSでは、`Version`は、現在のオペレーティングシステムのバージョンを示す文字列である `-[UIDevice systemVersion]`の結果です。システムバージョンの例は`10.3`です。たとえば、iOSのメジャーバージョン番号を検出するには：

```tsx
import {Platform} from 'react-native';

const majorVersionIOS = parseInt(Platform.Version, 10);
if (majorVersionIOS <= 9) {
  console.log('Work around a change in behavior');
}
```

## Platform-specific extensions

プラットフォーム固有のコードがより複雑な場合は、コードを別々のファイルに分割することを検討してください。React Native は、ファイルに`.ios`または`.android.`の拡張子が付いていることを検出し、他のコンポーネントから必要に応じて関連するプラットフォームファイルをロードします。

たとえば、プロジェクトに次のファイルがあるとします。

```shell
BigButton.ios.js
BigButton.android.js
```

その後、次のようにコンポーネントをインポートできます。

```tsx
import BigButton from './BigButton';
```

React Native は、実行中のプラットフォームに基づいて適切なファイルを自動的に選択します。

## Native-specific extensions (i.e. sharing code with NodeJS and Web)

モジュールをNodeJS/WebとReact Nativeの間で共有する必要があるが、Android/iOSの違いがない場合は、`.native.js`拡張子を使用することもできます。これは、React NativeとReactJSの間で共通のコードを共有しているプロジェクトに特に役立ちます。

たとえば、プロジェクトに次のファイルがあるとします。

```shell
Container.js # picked up by webpack, Rollup or any other Web bundler
Container.native.js # picked up by the React Native bundler for both Android and iOS (Metro)
```

次のように、`.native`拡張子を付けなくてもインポートできます。

```tsx
import Container from './Container';
```

**上級者向けのヒント：** プロダクションバンドルに未使用のコードが含まれないように、`.native.js`拡張子を無視するようにWebバンドラーを設定して、最終的なバンドルサイズを小さくしてください。
