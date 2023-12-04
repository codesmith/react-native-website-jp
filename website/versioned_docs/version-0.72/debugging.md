---
id: debugging
title: Debugging Basics
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## Accessing the Dev Menu

React Native は、いくつかのデバッグオプションを提供するアプリ内開発者メニューを提供します。開発メニューには、デバイスを振るか、キーボードショートカットでアクセスできます。

- iOSシミュレータ：<kbd>Cmd ⌘</kbd>+<kbd>D</kbd>（またはデバイス>シェイク）
- Androidのエミュレーター:<kbd>Cmd ⌘</kbd>+<kbd>M</kbd> (MacOS) または <kbd>Ctrl</kbd> + <kbd>M</kbd> (Windows および Linux)

また、Androidデバイスやエミュレータの場合は、ターミナルで `adb shell input keyevent 82`を実行することもできます。

![](/docs/assets/DevMenu.png)

:::note
リリース（プロダクション）ビルドでは、開発メニューは無効になっています。
:::

## LogBox

開発ビルドでのエラーと警告は、アプリ内のLogBoxに表示されます。

:::note
LogBoxはリリース（プロダクション）ビルドでは無効になっています。
:::

### Console Errors and Warnings

コンソールのエラーと警告は、画面上ではそれぞれ赤または黄色のバッジが付いた通知として、コンソールではエラーまたは警告の数が表示されます。コンソールのエラーや警告を表示するには、通知をタップしてログに関する全画面情報を表示し、コンソール内のすべてのログをページネーションします。

これらの通知は、`LogBox.ignoreAllLogs () `を使って隠すことができます。これは、たとえば製品のデモを行うときに役立ちます。さらに、通知は`LogBox.ignoreLogs()`を使用してログごとに非表示にできます。これは、サードパーティの依存関係にある警告のように、修正できないノイズの多い警告がある場合に役立ちます。

:::info
最後の手段としてログを無視し、無視されたログを修正するタスクを作成してください。
:::

```tsx
import {LogBox} from 'react-native';

// Ignore log notification by message:
LogBox.ignoreLogs(['Warning: ...']);

// Ignore all log notifications:
LogBox.ignoreAllLogs();
```

### Unhandled Errors

`undefined is not a function`などの未処理のJavaScriptエラーは、エラーの原因を示す全画面LogBoxエラーを自動的に開きます。これらのエラーは、無視して最小限に抑えることでエラーが発生したときのアプリの状態を確認できますが、必ず対処する必要があります。

### Syntax Errors

構文エラーが発生すると、全画面LogBoxエラーが自動的に開き、スタックトレースと構文エラーが発生した場所が表示されます。このエラーは無視できません。アプリを続行する前に修正する必要がある、不正なJavaScript実行を表しているためです。これらのエラーを非表示にするには、構文エラーを修正して、保存して自動的に閉じるか（高速更新が有効な場合）、<kbd>Cmd ⌘</kbd> / <kbd>Ctrl</kbd>+<kbd>R</kbd>を押してリロードします（高速更新が無効の場合）。

## Chrome Developer Tools

ChromeでJavaScriptコードをデバッグするには、開発メニューから「デバッガーを開く」を選択します。これにより、[http://localhost:8081/debugger-ui](http://localhost:8081/debugger-ui) に新しいタブが開きます。

ここから、Chromeメニューから「その他のツール」→「開発者ツール」を選択して [Chrome開発者ツール](https://developer.chrome.com/devtools) を開きます。または、ショートカット<kbd>⌥ Option</kbd> + <kbd>Cmd ⌘</kbd> + <kbd>I</kbd> (macOS) / <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>I</kbd> (Windows and Linux) を使用することもできます。

- Chrome DevToolsを初めて使用する場合は、ドキュメントの [Console](https://developer.chrome.com/docs/devtools/#console) タブと [Sources](https://developer.chrome.com/docs/devtools/#sources) タブについて知っておくことをお勧めします。
- デバッグしやすくするために、[Pause on Caught Exceptions](https://developer.chrome.com/docs/devtools/javascript/breakpoints/#exceptions) を有効にするとよいでしょう。

:::info
React Developer Tools というChrome拡張機能はReact Native では動作しませんが、代わりにスタンドアロンバージョンを使用できます。その方法については、[このセクション](react-devtools) を読んでください。
:::

:::note
Androidでは、デバッガーとデバイスの間の時間がずれると、アニメーションやイベントの動作などが正しく動作しない可能性があります。これは、`` adb shell "date `date +%m%d%H%M%Y.%S%3N`" `` を実行することで修正できます。物理デバイスを使用する場合は、ルートアクセスが必要です。
:::

### Debugging on a physical device

:::info
Expo CLIを使用している場合、これはすでに設定されています。
:::

<Tabs groupId="platform" defaultValue={constants.defaultPlatform} values={constants.platforms} className="pill-tabs">
<TabItem value="ios">

iOSデバイスでは、ファイル [`RCTWebSocketExecutor.mm`](https://github.com/facebook/react-native/blob/master/packages/react-native/React/CoreModules/RCTWebSocketExecutor.mm)を開き、"localhost"をコンピュータのIPアドレスに変更して、開発メニューから「JSをリモートでデバッグ」を選択します。

</TabItem>
<TabItem value="android">

USB経由で接続されたAndroid 5.0以降のデバイスでは、[`adb` コマンドラインツール](http://developer.android.com/tools/help/adb.html) を使用して、デバイスからコンピューターへのポート転送を設定できます。

```sh
adb reverse tcp:8081 tcp:8081
```

または、開発メニューから「設定」を選択し、お使いのコンピューターのIPアドレスと一致するように「デバイスのサーバーホストをデバッグ」設定を更新してください。

</TabItem>
</Tabs>

:::note
問題が発生した場合は、Chrome拡張機能の1つがデバッガーと予期しない方法で相互作用している可能性があります。すべての拡張機能を無効にして、問題のある拡張機能が見つかるまで1つずつ有効にしてみてください。
:::

<details>
<summary>Advanced: Debugging using a custom JavaScript debugger</summary>

Chromeデベロッパーツールの代わりにカスタムJavaScriptデバッガーを使用するには、`REACT_DEBUGGER` 環境変数を、カスタムデバッガーを起動するコマンドに設定します。その後、開発メニューから「デバッガーを開く」を選択してデバッグを開始できます。

デバッガーは、すべてのプロジェクトルートのリストをスペースで区切って受け取ります。たとえば、`REACT_DEBUGGER="node /path/to/launchDebugger.js --port 2345 --type ReactNative"` を設定した場合、コマンド`node /path/to/launchDebugger.js --port 2345 --type ReactNative /path/to/reactNative/app`がデバッガーの起動に使用されます。

:::note
この方法で実行されるカスタムデバッガーコマンドは、有効期間が短いプロセスで、200キロバイトを超える出力を生成しないようにする必要があります。
:::

</details>

## Safari Developer Tools

「JSをリモートでデバッグ」を有効にしなくても、Safariを使ってiOS版のアプリをデバッグできます。

- 物理デバイスで、`Settings → Safari → Advanced`まで移動し、"Web Inspector"がオンになっていることを確認してください。（この手順はシミュレータでは必要ありません）
- MacでSafariの [開発] メニューを有効にします：`Settings... (or Preferences...) → Advanced → Select "Show Develop menu in menu bar"`
-アプリのJSContextを選択してください：`Develop → Simulator (or other device) → JSContext`
-コンソールとデバッガがあるSafariのWebインスペクタが開くはずです

ソースマップはデフォルトでは有効になっていないかもしれませんが、[このガイド](http://blog.nparashuram.com/2019/10/debugging-react-native-ios-apps-with.html) または [ビデオ](https://www.youtube.com/watch?v=GrGqIIz51k4) に従ってソースマップを有効にし、ソースコードの適切な場所にブレークポイントを設定してください。

ただし、アプリがリロードされるたびに（ライブリロードを使用するか、手動でリロードして）、新しいJSContextが作成されます。"Automatically Show Web Inspectors for JSContexts"を選択すると、最新のJSContextを手動で選択する必要がなくなります。

## Performance Monitor

開発メニューで"Perf Monitor"を選択すると、パフォーマンスの問題をデバッグするのに役立つパフォーマンスオーバーレイを有効にできます。
