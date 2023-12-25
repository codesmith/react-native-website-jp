---
id: hermes
title: Using Hermes
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<a href="https://hermesengine.dev">
  <img width={300} height={300} className="hermes-logo" src="/docs/assets/HermesLogo.svg" style={{height: "auto"}}/>
</a>

[Hermes](https://hermesengine.dev) はReact Native 向けに最適化されたオープンソースの JavaScript エンジンです。多くのアプリでは、Hermesを使用すると、JavaScriptCoreと比較して、起動時間が短縮され、メモリ使用量が減少し、アプリサイズが小さくなります。
React Native ではデフォルトで Hermes が使用されており、有効化するために追加の設定は必要ありません。

## Bundled Hermes

React Native にはエルメスの**バンドルバージョン**が付属しています。
React Native の新しいバージョンがリリースされるたびに、Hermesのバージョンをビルドします。これにより、使用しているReact Native のバージョンと完全に互換性のあるバージョンのHermesを使用していることを確認できます。

これまで、HermesのバージョンをReact Native のバージョンと一致させるのに問題がありました。これにより、この問題は完全に解消され、特定の React Native バージョンと互換性のある JS エンジンがユーザーに提供されます。

この変更は React Native のユーザーには完全に分かりやすいものです。このページで説明されているコマンドを使用して Hermes を無効にすることは引き続き可能です。
[You can read more about the technical implementation on this page.](/architecture/bundled-hermes)

## Confirming Hermes is in use

最近新しいアプリをゼロから作成した場合は、ウェルカムビューでHermesが有効になっているかどうかを確認してください。

![Where to find JS engine status in AwesomeProject](/docs/assets/HermesApp.jpg)

Hermes が使用されていることを確認するのに使用できる `HermesInternal` グローバル変数が JavaScript で利用できるようになります。

```jsx
const isHermes = () => !!global.HermesInternal;
```

:::caution
標準的でない JS バンドルのロード方法を使用している場合は、`HermesInternal` 変数は使用できても、高度に最適化されたプリコンパイル済みバイトコードを使用していない可能性があります。
`.hbc` ファイルを使用していることを確認し、以下に詳述するように、ビフォー/アフターのベンチマークも行ってください。
:::

Hermesの利点を確認するには、アプリのリリースビルド/デプロイを作成して比較してみてください。例えば; プロジェクトのルートから:

<Tabs groupId="platform" queryString defaultValue={constants.defaultPlatform} values={constants.platforms} className="pill-tabs">
<TabItem value="android">

[//]: # 'Android'

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run android -- --mode="release"
```

</TabItem>
<TabItem value="yarn">

```shell
yarn android --mode release
```

</TabItem>
</Tabs>

</TabItem>
<TabItem value="ios">

[//]: # 'iOS'

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run ios -- --mode="Release"
```

</TabItem>
<TabItem value="yarn">

```shell
yarn ios --mode Release
```

</TabItem>
</Tabs>

</TabItem>
</Tabs>

これにより、ビルド時に JavaScript がバイトコードにコンパイルされ、デバイスでのアプリの起動速度が向上します。

## Debugging JS on Hermes using Google Chrome's DevTools

エルメスは Chrome インスペクタープロトコルを実装することで Chrome デバッガーをサポートしています。つまり、Chrome のツールを使用して、Hermes、エミュレーター、または実際の物理デバイスで実行されている JavaScript を直接デバッグできるということです。

:::info
[Debugging](debugging#debugging-using-a-custom-javascript-debugger) セクションに記載されているアプリ内開発メニューの「リモート JS デバッグ」とは大きく異なる点に注意してください。実際には、開発マシン（ラップトップまたはデスクトップ）の Chrome V8 で JS コードを実行します。
:::

ChromeはMetro経由でデバイス上で動作しているHermesに接続するため、Metroがどこでリスニングしているかを知る必要があります。通常は `localhost:8081` ですが、これは [configurable](https://facebook.github.io/metro/docs/configuration) です。`yarn start` を実行すると、アドレスは起動時に stdout に書き込まれます。

Metro サーバーがリッスンしている場所がわかったら、次の手順で Chrome に接続できます。

1. Chrome ブラウザインスタンスで `chrome://inspect` に移動します。

2. `Configure...` ボタンを使用して Metro サーバーのアドレスを追加します (通常は、前述のように `localhost:8081`)。

![Configure button in Chrome DevTools devices page](/docs/assets/HermesDebugChromeConfig.png)

![Dialog for adding Chrome DevTools network targets](/docs/assets/HermesDebugChromeMetroAddress.png)

3. これで、「Hermes React Native」ターゲットと「inspect」リンクが表示され、これを使用してデバッガーを起動できます。「inspect」リンクが表示されない場合は、Metroサーバーが稼働していることを確認してください。![Target inspect link](/docs/assets/HermesDebugChromeInspect.png)

4. Chrome のデバッグツールを使用できるようになりました。たとえば、次回JavaScriptが実行されたときにブレークポイントを設定するには、一時停止ボタンをクリックして、JavaScriptを実行するアクションをアプリ内でトリガーします。![Pause button in debug tools](/docs/assets/HermesDebugChromePause.png)

## Enabling Hermes on Older Versions of React Native

React Native 0.70 時点では、エルメスがデフォルトのエンジンとなっています。このセクションでは、古いバージョンの React Native で Hermes を有効にする方法について説明します。
まず、Androidでエルメスを有効にするにはReact Native のバージョン0.60.4以上、iOSでエルメスを有効にするにはReact Native のバージョン0.64以上を使用していることを確認してください。

React Native の以前のバージョンをベースにした既存のアプリがある場合は、まずそれをアップグレードする必要があります。その方法については [Upgrading to new React Native Versions](/docs/upgrading) を参照してください。アプリをアップグレードしたら、Hermesに切り替える前にすべてが機能することを確認してください。

:::caution Note for React Native compatibility
Hermesの各リリースは、特定のRNバージョンを対象としています。経験則では、常に [Hermes releases](https://github.com/facebook/hermes/releases) に厳密に従うことです。
バージョンの不一致により、最悪の場合、アプリが即座にクラッシュする可能性があります。
:::

:::info Note for Windows users
エルメスには [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/en-us/download/details.aspx?id=48145) が必要です。
:::

### Android

`android/gradle.properties` ファイルを編集して `hermesEnabled` が正しいことを確認してください:

```diff
# Use this property to enable or disable the Hermes JS engine.
# If set to false, you will be using JSC instead.
hermesEnabled=true
```

:::note
このプロパティはReact Native 0.71 で追加されました。`gradle.properties` ファイルに見つからない場合は、使用している対応する React Native バージョンのドキュメントを参照してください。
:::

また、ProGuard を使用している場合は、`proguard-rules.pro` に次のルールを追加する必要があります。

```
-keep class com.facebook.hermes.unicode.** { *; }
-keep class com.facebook.jni.** { *; }
```

次に、アプリを少なくとも 1 回ビルドしたことがある場合は、ビルドをクリーンアップします。

```shell
$ cd android && ./gradlew clean
```

以上です！これで、通常どおりアプリを開発してデプロイできるはずです。

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run android
```

</TabItem>
<TabItem value="yarn">

```shell
yarn android
```

</TabItem>
</Tabs>

### iOS

React Native 0.64 以降、エルメスは iOS でも動作します。Hermes for iOS を有効にするには、`ios/Podfile` ファイルを編集し、下図のように変更します。

```diff
   use_react_native!(
     :path => config[:reactNativePath],
     # to enable hermes on iOS, change `false` to `true` and then install pods
     # By default, Hermes is disabled on Old Architecture, and enabled on New Architecture.
     # You can enable/disable it manually by replacing `flags[:hermes_enabled]` with `true` or `false`.
-    :hermes_enabled => flags[:hermes_enabled],
+    :hermes_enabled => true
   )
```

新しいアーキテクチャを使用している場合は、デフォルトで Hermes を使用します。このような値を指定することで
`true` または `false` として、必要に応じてエルメスを有効/無効にできます。

設定が完了したら、次の方法でHermesポッドをインストールできます。

```shell
$ cd ios && pod install
```

以上です！これで、通常どおりアプリを開発してデプロイできるはずです。

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run ios
```

</TabItem>
<TabItem value="yarn">

```shell
yarn ios
```

</TabItem>
</Tabs>

## Switching back to JavaScriptCore

React Native は JavaScriptCore を [JavaScript engine](javascript-environment) として使用することもサポートしています。Hermesをオプトアウトするには、こちらの指示に従ってください。

### Android

`android/gradle.properties` ファイルを編集して `hermesEnabled` を false に戻してください:

```diff
# Use this property to enable or disable the Hermes JS engine.
# If set to false, you will be using JSC instead.
hermesEnabled=false
```

### iOS

`ios/Podfile` ファイルを編集し、下図のように変更します。

```diff
   use_react_native!(
     :path => config[:reactNativePath],
     # Hermes is now enabled by default. Disable by setting this flag to false.
     # Upcoming versions of React Native may rely on get_default_flags(), but
     # we make it explicit here to aid in the React Native upgrade process.
-    :hermes_enabled => flags[:hermes_enabled],
+    :hermes_enabled => false,
   )
```
