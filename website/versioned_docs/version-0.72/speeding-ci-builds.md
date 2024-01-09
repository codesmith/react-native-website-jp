---
id: speeding-ci-builds
title: Speeding Up CI Builds
---

あなたやあなたの会社は React Native アプリケーションをテストするために継続的インテグレーション (CI) 環境を設定しているかもしれません。

高速な CI サービスは次の 2 つの理由で重要です。

- CI マシンの稼働時間が長くなるほど、コストは高くなります。
- CI ジョブの実行に時間がかかるほど、開発ループは長くなります。

そのため、CI 環境が React Native の構築に費やす時間を最小限に抑えることが重要です。

## Disable Flipper for iOS

[Flipper](https://github.com/facebook/flipper) は React Native にデフォルトで付属しているデバッグツールで、開発者が React Native アプリケーションをデバッグしてプロファイリングするのに役立ちます。ただし、CI では Flipper は必須ではありません。CI 環境でビルドされたアプリを自分や同僚がデバッグする必要はほとんどありません。

iOSアプリの場合、FlipperはReact Native フレームワークがビルドされるたびにビルドされるため、ビルドに時間がかかる場合がありますが、この時間を節約できます。

React Native 0.71 から、テンプレートのポッドファイルに [`NO_FLIPPER` flag](https://github.com/facebook/react-native/blob/main/packages/react-native/template/ios/Podfile#L20) という新しいフラグを導入しました。

デフォルトでは `NO_FLIPPER` フラグは設定されていないため、Flipperはデフォルトでアプリに含まれます。

iOS ポッドをインストールするときに `NO_FLIPPER=1` を指定して、React Native に Flipper をインストールしないように指示できます。通常、コマンドは次のようになります。

```shell
# from the root folder of the react native project
NO_FLIPPER=1 bundle exec pod install --project-directory=ios
```

このコマンドを CI 環境に追加すると、Flipper の依存関係のインストールを省略できるため、時間とコストを節約できます。

### Handle Transitive Dependencies

アプリが Flipper ポッドに依存するいくつかのライブラリを使用している可能性があります。その場合は、`NO_FLIPPER` フラグで flipper を無効にするだけでは不十分な場合があります。この場合、アプリのビルドが失敗する可能性があります。

このような場合に対処する適切な方法は、react nativeのカスタム設定を追加して、アプリに推移的な依存関係を正しくインストールするように指示することです。これを実現するには:

1. まだ持っていない場合は、`react-native.config.js` という名前の新しいファイルを作成してください。
2. フラグがオンになっている場合は、`dependencies` から推移的な依存関係を明示的に除外します。

たとえば、`react-native-flipper` ライブラリは Flipper に依存する追加ライブラリです。アプリでこれを使用している場合は、依存関係から除外する必要があります。`react-native.config.js` は次のようになります。

```js title="react-native.config.js"
module.exports = {
  // other fields
  dependencies: {
    ...(process.env.NO_FLIPPER
      ? {'react-native-flipper': {platforms: {ios: null}}}
      : {}),
  },
};
```
