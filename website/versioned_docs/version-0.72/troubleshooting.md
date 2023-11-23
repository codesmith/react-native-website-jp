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

In the project's build settings, `User Search Header Paths` and `Header Search Paths` are two configs that specify where Xcode should look for `#import` header files specified in the code. For Pods, CocoaPods uses a default array of specific folders to look in. Verify that this particular config is not overwritten, and that none of the folders configured are too large. If one of the folders is a large folder, Xcode will attempt to recursively search the entire directory and throw above error at some point.

To revert the `User Search Header Paths` and `Header Search Paths` build settings to their defaults set by CocoaPods - select the entry in the Build Settings panel, and hit delete. It will remove the custom override and return to the CocoaPod defaults.

### No transports available

React Native implements a polyfill for WebSockets. These [polyfills](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Core/InitializeCore.js) are initialized as part of the react-native module that you include in your application through `import React from 'react'`. If you load another module that requires WebSockets, such as [Firebase](https://github.com/facebook/react-native/issues/3645), be sure to load/require it after react-native:

```
import React from 'react';
import Firebase from 'firebase';
```

## Shell Command Unresponsive Exception

If you encounter a ShellCommandUnresponsiveException exception such as:

```
Execution failed for task ':app:installDebug'.
  com.android.builder.testing.api.DeviceException: com.android.ddmlib.ShellCommandUnresponsiveException
```

Try [downgrading your Gradle version to 1.2.3](https://github.com/facebook/react-native/issues/2720) in `android/build.gradle`.

## react-native init hangs

If you run into issues where running `npx react-native init` hangs in your system, try running it again in verbose mode and referring to [#2797](https://github.com/facebook/react-native/issues/2797) for common causes:

```shell
npx react-native init --verbose
```

When you're debugging a process or need to know a little more about the error being thrown, you may want to use the verbose option to output more logs and information to nail down your issue.

Run the following command in your project's root directory.

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

Issue caused by the number of directories [inotify](https://github.com/guard/listen/wiki/Increasing-the-amount-of-inotify-watchers) (used by watchman on Linux) can monitor. To solve it, run this command in your terminal window

```shell
echo fs.inotify.max_user_watches=582222 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```

### Error: spawnSync ./gradlew EACCES

If you run into issue where executing `npm run android` or `yarn android` on macOS throws the above error, try to run `sudo chmod +x android/gradlew` command to make `gradlew` files into executable.

[metro]: https://facebook.github.io/metro/
