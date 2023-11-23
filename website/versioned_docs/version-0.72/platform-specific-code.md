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

また、「Platform.select」メソッドもあります。これは、キーが`'ios' | 'android' | 'native' | 'default'`のいずれかであるオブジェクトを指定すると、現在実行しているプラットフォームに最も適した値を返します。つまり、携帯電話で実行している場合は、`ios`キーと`android`キーが優先されます。それらが指定されていない場合は、`native`キーが使用され、次に`default`キーが使用されます。

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

On Android, the `Platform` module can also be used to detect the version of the Android Platform in which the app is running:

```tsx
import {Platform} from 'react-native';

if (Platform.Version === 25) {
  console.log('Running on Nougat!');
}
```

**Note**: `Version` is set to the Android API version not the Android OS version. To find a mapping please refer to [Android Version History](https://en.wikipedia.org/wiki/Android_version_history#Overview).

### Detecting the iOS version

On iOS, the `Version` is a result of `-[UIDevice systemVersion]`, which is a string with the current version of the operating system. An example of the system version is "10.3". For example, to detect the major version number on iOS:

```tsx
import {Platform} from 'react-native';

const majorVersionIOS = parseInt(Platform.Version, 10);
if (majorVersionIOS <= 9) {
  console.log('Work around a change in behavior');
}
```

## Platform-specific extensions

When your platform-specific code is more complex, you should consider splitting the code out into separate files. React Native will detect when a file has a `.ios.` or `.android.` extension and load the relevant platform file when required from other components.

For example, say you have the following files in your project:

```shell
BigButton.ios.js
BigButton.android.js
```

You can then import the component as follows:

```tsx
import BigButton from './BigButton';
```

React Native will automatically pick up the right file based on the running platform.

## Native-specific extensions (i.e. sharing code with NodeJS and Web)

You can also use the `.native.js` extension when a module needs to be shared between NodeJS/Web and React Native but it has no Android/iOS differences. This is especially useful for projects that have common code shared among React Native and ReactJS.

For example, say you have the following files in your project:

```shell
Container.js # picked up by webpack, Rollup or any other Web bundler
Container.native.js # picked up by the React Native bundler for both Android and iOS (Metro)
```

You can still import it without the `.native` extension, as follows:

```tsx
import Container from './Container';
```

**Pro tip:** Configure your Web bundler to ignore `.native.js` extensions in order to avoid having unused code in your production bundle, thus reducing the final bundle size.
