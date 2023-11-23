---
id: troubleshooting
title: Troubleshooting
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

これらは、React Native のセットアップ中に遭遇する可能性のある一般的な問題です。ここに記載されていない問題に遭遇した場合は、[GitHubでissueを探して](https://github.com/facebook/react-native/issues/) みてください。

### Port already in use

[Metro bundler][metro] はポート8081で動作します。別のプロセスがすでにそのポートを使用している場合は、そのプロセスを終了するか、バンドラーが使用するポートを変更することができます。

#### Terminating a process on port 8081

次のコマンドを実行して、ポート8081でリッスンしているプロセスのIDを見つけます。

```shell
sudo lsof -i :8081
```

次に、以下を実行してプロセスを終了します。

```shell
kill -9 <PID>
```

Windowsでは、[リソースモニター](https://stackoverflow.com/questions/48198/how-can-you-find-out-which-process-is-listening-on-a-port-on-windows) を使用してポート8081を使用しているプロセスを検索し、タスクマネージャーを使用してプロセスを停止できます。

#### Using a port other than 8081

プロジェクトのルートから `port`パラメータを使用して、8081以外のポートを使用するようにバンドラーを構成できます。実行してください。

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm start -- --port=8088
```

</TabItem>
<TabItem value="yarn">

```shell
yarn start --port 8088
```

</TabItem>
</Tabs>

また、新しいポートからJavaScriptバンドルを読み込むには、アプリケーションを更新する必要があります。Xcodeからデバイス上で実行している場合は、`ios/__App_Name__.xcodeproj/project.pbxproj`ファイル内における`8081`の記載箇所を選択したポートに更新することで実現できます。

### NPM locking error

React Native CLIを使用しているときに`npm WARN locking Error: EACCES`などのエラーが発生した場合は、以下を実行してみてください。

```shell
sudo chown -R $USER ~/.npm
sudo chown -R $USER /usr/local/lib/node_modules
```

### Missing libraries for React

React Native をプロジェクトに手動で追加した場合は、`RCTText.xcodeproj`、 `RCTImage.xcodeproj`など、使用中の、関連する全ての依存関係が含まれていることを確認してください。次に、これらの依存関係によって構築されたバイナリをアプリのバイナリにリンクする必要があります。Xcodeプロジェクト設定の`リンクされたフレームワークとバイナリ`セクションを使用してください。より詳細な手順は次のとおりです:[ライブラリのリンク](linking-libraries-ios.md#content)。

CocoaPodsを使用している場合は、サブスペックとともにReactを`Podfile`に追加していることを確認してください。たとえば、`<Text />`、`<Image />`および `fetch ()` APIを使用していたら、これらを `Podfile` に追加する必要があります。

```
pod 'React', :path => '../node_modules/react-native', :subspecs => [
  'RCTText',
  'RCTImage',
  'RCTNetwork',
  'RCTWebSocket',
]
```

次に、`pod install`を実行し、Reactがインストールされたプロジェクトに `Pods/`ディレクトリが作成されていることを確認します。CocoaPods は、今後、これらのインストールされた依存関係を使用できるように、生成された `.xcworkspace` ファイルを使用するように指示します。

#### React Native does not compile when being used as a CocoaPod

[cocoapods-fix-react-native](https://github.com/orta/cocoapods-fix-react-native) というCocoaPodsプラグインがあります。これは、依存関係マネージャーを使用する場合の違いによるソースコードのポストフィックスに対処します。

#### Argument list too long: recursive header expansion failed

プロジェクトのビルド設定では、`User Search Header Paths` と `Header Search Paths`は、コードで指定されている`#import`ヘッダーファイルをXcodeが探す場所を指定する2つの構成です。ポッドの場合、CocoaPodsは特定のフォルダーのデフォルトの配列を使用して検索します。この特定の構成が上書きされていないこと、および構成されたフォルダが大きすぎないことを確認してください。フォルダの1つが大きなフォルダの場合、Xcodeはディレクトリ全体を再帰的に検索し、ある時点で上記のエラーを投げます。

`User Search Header Paths` と `Header Search Paths` のビルド設定をCocoaPodsが設定したデフォルトに戻すには、ビルド設定パネルでエントリを選択し、削除をクリックします。カスタムオーバーライドが削除され、CocoaPodのデフォルトに戻ります。

### No transports available

React Native はWebSocket用のポリフィルを実装しています。これらの [polyfills](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Core/InitializeCore.js) は、`import React from 'react'`でアプリケーションに組み込むreact-nativeモジュールの一部として初期化されます。[Firebase](https://github.com/facebook/react-native/issues/3645) など、WebSocketを必要とする別のモジュールをロードする場合は、必ずreact-nativeの後にload/requireしてください。

```
import React from 'react';
import Firebase from 'firebase';
```

## Shell Command Unresponsive Exception

次のようなShellCommandUnresponsiveException例外に遭遇した場合：

```
Execution failed for task ':app:installDebug'.
  com.android.builder.testing.api.DeviceException: com.android.ddmlib.ShellCommandUnresponsiveException
```

`android/build.gradle` で [Gradleのバージョンを1.2.3にダウングレード](https://github.com/facebook/react-native/issues/2720) を試してみてください。

## react-native init hangs

`npx react-native init`を実行するとシステムでハングする問題が発生した場合は、verbose mode でもう一度実行して、一般的な原因については [#2797](https://github.com/facebook/react-native/issues/2797) を参照してください。

```shell
npx react-native init --verbose
```

プロセスをデバッグしているときや、throw されるエラーについてもう少し知りたいときは、verboseオプションを使用して、問題を突き止めるためにより多くのログと情報を出力したいと思うかもしれません。

プロジェクトのルートディレクトリで次のコマンドを実行します。

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run android -- --verbose
```

</TabItem>
<TabItem value="yarn">

```shell
yarn android --verbose
```

</TabItem>
</Tabs>

## Unable to start react-native package manager (on Linux)

### Case 1: Error "code":"ENOSPC","errno":"ENOSPC"

[inotify](https://github.com/guard/listen/wiki/Increasing-the-amount-of-inotify-watchers) (Linuxのウォッチマンが使用) が監視できるディレクトリの数が原因で問題が発生しました。これを解決するには、ターミナルウィンドウで次のコマンドを実行してください

```shell
echo fs.inotify.max_user_watches=582222 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```

### Error: spawnSync ./gradlew EACCES

MacOSで`npm run android`または`yarn android`を実行すると上記のエラーが発生する問題が発生した場合は、`sudo chmod +x android/gradlew`コマンドを実行して`gradlew`ファイルを実行可能ファイルにしてみてください。

[metro]: https://facebook.github.io/metro/
