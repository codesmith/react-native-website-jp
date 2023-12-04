---
id: react-devtools
title: React Developer Tools
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

[スタンドアロン版のReact開発者ツール](https://github.com/facebook/react/tree/main/packages/react-devtools) を使用して、Reactコンポーネント階層をデバッグできます。これを使用するには、`react-devtools`パッケージをグローバルにインストールしてください。

<Tabs groupId="package-manager" defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm install -g react-devtools
```

</TabItem>
<TabItem value="yarn">

```shell
yarn global add react-devtools
```

</TabItem>
</Tabs>

次に、ターミナルから「react-devtools」を実行して、スタンドアロンの開発ツールアプリを起動します。数秒以内にシミュレータに接続するはずです。

```shell
react-devtools
```

![React DevTools](/docs/assets/ReactDevTools.png)

:::info
グローバルインストールを避けたい場合は、プロジェクトの依存関係として `react-devtools`を追加できます。`npm install --save-dev react-devtools` を使用してプロジェクトに `react-devtools` パッケージを追加し、次に`package.json`の`scripts`セクションに`react-devtools`を追加し、プロジェクトフォルダから `npm run react-devtools` を実行して開発ツールを開きます。
:::

## Integration with React Native Inspector

開発メニューを開き、「インスペクターの切り替え」を選択します。任意のUI要素をタップしてその情報を表示できるオーバーレイが表示されます。

![React Native Inspector](/docs/assets/Inspector.gif)

ただし、`react-devtools`が実行されていると、インスペクターは折りたたまれたモードになり、代わりに開発ツールをプライマリUIとして使用します。このモードでは、シミュレータ内の何かをクリックすると、DevToolsの関連コンポーネントが表示されます。

![React DevTools Inspector Integration](/docs/assets/ReactDevToolsInspector.gif)

同じメニューで [インスペクターの切り替え] を選択して、このモードを終了できます。

## Debugging application state

[Reactotron](https://github.com/infinitered/reactotron) はオープンソースのデスクトップアプリで、ReduxまたはMOBX状態ツリーのアプリケーションの状態を検査したり、カスタムログを表示したり、状態のリセットなどのカスタムコマンドを実行したり、state のスナップショットを保存および復元したり、React Native アプリのその他の便利なデバッグ機能を実行したりできます。

インストール手順は [README](https://github.com/infinitered/reactotron) で確認できます。Expoを使っているなら、こちらに [Expoへのインストール方法](https://shift.infinite.red/start-using-reactotron-in-your-expo-project-today-in-3-easy-steps-a03d11032a7a)の詳細が記載された記事があります。

## Troubleshooting

:::tip
React DevToolsを起動したら、指示に従ってください。React DevToolsを開く前にアプリケーションを実行していた場合は、[開発者メニューを開く](/docs/debugging#accessing-the-dev-menu)して接続する必要があるかもしれません。

![React DevTools Connection](/docs/assets/ReactDevToolsConnection.gif)
:::

:::info
エミュレータへの接続で不具合がある場合（特にAndroid 12）、新しいターミナルで`adb reverse tcp:8097 tcp:8097`を実行してみてください。
:::
