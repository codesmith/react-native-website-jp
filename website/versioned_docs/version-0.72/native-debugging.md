---
id: native-debugging
title: Native Debugging
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<div className="banner-native-code-required">
  <h3>Projects with Native Code Only</h3>
  <p>
    次のセクションは、ネイティブコードが公開されているプロジェクトにのみ適用されます。マネージドなExpoワークフローを使用している場合は、このAPIを使用する際に<a href="https://docs.expo.dev/workflow/prebuild/" target="_blank">プレビルド</a>のガイドを参照してください。
  </p>
</div>

## Accessing native logs

iOSまたはAndroidアプリのコンソールログは、アプリの実行中にターミナルで次のコマンドを使用して表示できます。
```shell
# For Android:
npx react-native log-android
# Or, for iOS:
npx react-native log-ios
```

これらには、iOSシミュレータの [デバッグ] > [システムログを開く...] から、またはAndroidアプリがデバイスまたはエミュレータで実行されているときにターミナルで `adb logcat "*:S" ReactNative:V ReactNativeJS:V` を実行してアクセスすることもできます。

:::info
Expo CLIを使用している場合、コンソールログはバンドラーと同じ端末出力にすでに表示されています。
:::

## Debugging native code

ネイティブモジュールを書くときなど、ネイティブコードを扱うときは、Android StudioまたはXcodeからアプリを起動して、標準のネイティブアプリを構築する場合と同じように、ネイティブのデバッグ機能（ブレークポイントの設定など）を利用できます。

別の選択肢は、React Native CLIを使用してアプリケーションを実行し、ネイティブIDE（Android StudioまたはXcode）のネイティブデバッガをプロセスにアタッチすることです。

### Android Studio

Android Studioでは、メニューバーの [実行] オプションに移動し、[プロセスに添付...] をクリックして、実行中のReact Native アプリを選択することでこれを実行できます。

### Xcode

Xcodeでは、上部のメニューバーの「デバッグ」をクリックし、「プロセスに添付」オプションを選択して、「可能性の高いターゲット」のリストからアプリケーションを選択します。
