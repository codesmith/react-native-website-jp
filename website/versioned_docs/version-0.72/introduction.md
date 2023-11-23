---
id: getting-started
title: Introduction
description: This helpful guide lays out the prerequisites for learning React Native, using these docs, and setting up your environment.
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<div className="content-banner">
  React Nativeの旅の始まりへようこそ！環境の設定方法についての記述は<a href="environment-setup">こちらのセクション</a>に移動しました。ドキュメント、ネイティブコンポーネント、Reactなどの導入については、引き続き読み進めてください！
  <img className="content-banner-img" src="/docs/assets/p_android-ios-devices.svg" alt=" " />
</div>

上級iOS開発者からReact初心者、キャリアで初めてプログラミングを始める人まで、さまざまな種類の人々がReact Nativeを使用しています。これらのドキュメントは、経験レベルや経歴に関係なく、すべての学習者向けに書かれています。

## これらのドキュメントの使い方

ここから始めて、本のようにこれらのドキュメントを直線的に読むこともできますし、必要な特定のセクションを読むこともできます。Reactにはもう慣れていますか？[そのセクション](intro-react) を飛ばしても良いですし、少し復習するために読むこともできます。

## 前提条件

リアクトネイティブを使用するには、JavaScriptの基本を理解している必要があります。JavaScriptを初めて使用する場合や復習が必要な場合は、Mozillaデベロッパーネットワークで [ダイブイン](https://developer.mozilla.org/en-US/docs/Web/JavaScript) もしくは [ブラッシュアップ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript) ができます。

> React、Android、iOSの開発に関する予備知識がないことを前提に最善を尽くしていますが、これらは意欲的なReact Native開発者にとって貴重な学習トピックです。必要に応じて、より詳細なリソースや記事へのリンクを張っています。

## インタラクティブな例

このイントロダクションでは、次のようなインタラクティブな例を使ってブラウザですぐに始められます：

```SnackPlayer name=Hello%20World
import React from 'react';
import {Text, View} from 'react-native';

const YourApp = () => {
  return (
    <View
      style={{
        flex: 1,
        justifyContent: 'center',
        alignItems: 'center',
      }}>
      <Text>Try editing me! 🎉</Text>
    </View>
  );
};

export default YourApp;
```

上の記述はSnack Playerです。React Nativeプロジェクトを埋め込んで実行し、AndroidやiOSなどのプラットフォームでどのようにレンダリングされるかを共有するために、Expoによって作成された便利なツールです。コードはライブで編集できるので、ブラウザで直接試すことができます。さっそく上記の「"Try editing me!"」というテキストを「"Hello, world!"」に変えてみてください！

> オプションで、ローカル開発環境をセットアップする場合は、[ローカルマシンで環境を設定するためのガイド](environment-setup)に従ってください。そして、そこにある「App.js」ファイルにコード例を貼り付けます。（あなたがウェブ開発者なら、モバイルブラウザのテスト用にローカル環境をすでにセットアップしているかもしれません！）

## Developer Notes

さまざまな開発背景を持つ人々がReact Nativeを学んでいます。ウェブからAndroid、iOSなど、さまざまなテクノロジーの経験があるかもしれません。私たちは、あらゆるバックグラウンドの開発者のために書くようにしています。時々、私たちはあるプラットフォームや別のプラットフォームに固有の説明をすることがあります：

<Tabs groupId="guide" queryString defaultValue="web" values={constants.getDevNotesTabs(["android","ios","web"])}>

<TabItem value="android">

> Android開発者はこの概念に精通しているかもしれません。

</TabItem>
<TabItem value="ios">

> iOSの開発者はこの概念に精通しているかもしれません。

</TabItem>
<TabItem value="web">

>Web開発者はこの概念に精通しているかもしれません。

</TabItem>
</Tabs>

## Formatting

メニューパスは太字で書かれていて、サブメニューを移動するにはキャレットを使います。例: **Android Studio > Preferences**

---

このガイドの仕組みがわかったので、今度はReact Nativeの基礎について学びましょう： [Native Components](intro-react-native-components.md)。
