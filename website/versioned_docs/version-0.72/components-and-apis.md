---
id: components-and-apis
title: Core Components and APIs
---
 
React Nativeには、アプリですぐに使用できる組み込みの [Core Components](intro-react-native-components) が多数用意されています。それらはすべて左側のサイドバー（または画面が狭い場合は上のメニュー）にあります。どこから始めたらよいかわからない場合は、次のカテゴリを見てください。

- [Basic Components](components-and-apis#basic-components)
- [User Interface](components-and-apis#user-interface)
- [List Views](components-and-apis#list-views)
- [Android-specific](components-and-apis#android-components-and-apis)
- [iOS-specific](components-and-apis#ios-components-and-apis)
- [Others](components-and-apis#others)

React NativeにバンドルされているコンポーネントやAPIに限定されません。React Nativeには何千人もの開発者のコミュニティがあります。特定のことを行うライブラリを探している場合は、[ライブラリの検索に関するガイド](libraries#finding-libraries) を参照してください。

## Basic Components

 ほとんどのアプリは、最終的にこれらの基本コンポーネントのいずれかを使用します。

<div className="component-grid component-grid-border">
  <div className="component">
    <a href="./view">
      <h3>View</h3>
      <p>UIを構築するための最も基本的なコンポーネントです。</p>
    </a>
  </div>
  <div className="component">
    <a href="./text">
      <h3>Text</h3>
      <p>テキストを表示するためのコンポーネントです。</p>
    </a>
  </div>
  <div className="component">
    <a href="./image">
      <h3>Image</h3>
      <p>画像を表示するためのコンポーネントです。</p>
    </a>
  </div>
  <div className="component">
    <a href="./textinput">
      <h3>TextInput</h3>
      <p>キーボードを使ってアプリにテキストを入力するためのコンポーネント。</p>
    </a>
  </div>
  <div className="component">
    <a href="./scrollview">
      <h3>ScrollView</h3>
      <p>複数のコンポーネントとビューをホストできるスクロールコンテナを提供します。</p>
    </a>
  </div>
  <div className="component">
    <a href="./stylesheet">
      <h3>StyleSheet</h3>
      <p>CSSスタイルシートに似た抽象化レイヤーを提供します。</p>
    </a>
  </div>
</div>

## User Interface

 ユーザーインターフェイスこれらの一般的なユーザーインターフェイスコントロールは、どのプラットフォームでもレンダリングできます。

<div className="component-grid component-grid-border">
  <div className="component">
    <a href="./button">
      <h3>ボタン</h3>
      <p>どのプラットフォームでもうまく表示されるはずのタッチを処理するための基本的なボタンコンポーネントです。</p>
    </a>
  </div>
  <div className="component">
    <a href="./switch">
      <h3>スイッチ</h3>
      <p>ブーリアン入力をレンダリングします。</p>
    </a>
  </div>
</div>

## List Views

より一般的に使用される[`ScrollView`](./scrollview)とは異なり、次のリストビューコンポーネントは、現在画面に表示されている要素のみをレンダリングします。これにより、長いデータリストを表示する場合にパフォーマンスが向上します。

<div className="component-grid component-grid-border">
  <div className="component">
    <a href="./flatlist">
      <h3>FlatList</h3>
      <p>パフォーマンスの高いスクロール可能なリストをレンダリングするためのコンポーネント。</p>
    </a>
  </div>
  <div className="component">
    <a href="./sectionlist">
      <h3>SectionList</h3>
      <p><code>FlatList</code>と似ていますが、セクションリスト用です。</p>
    </a>
  </div>
</div>

## Android Components and APIs

 次のコンポーネントの多くは、一般的に使用されるAndroidクラスのラッパーを提供します。

<div className="component-grid component-grid-border">
  <div className="component">
    <a href="./backhandler">
      <h3>BackHandler</h3>
      <p>戻るナビゲーションのためのハードウェアボタンの押下を検出します。</p>
    </a>
  </div>
  <div className="component">
    <a href="./drawerlayoutandroid">
      <h3>DrawerLayoutAndroid</h3>
      <p>Androidで<code>DrawerLayout</code>をレンダリングします。</p>
    </a>
  </div>
  <div className="component">
    <a href="./permissionsandroid">
      <h3>PermissionsAndroid</h3>
      <p>Android Mで導入された権限モデルへのアクセスを提供します。</p>
    </a>
  </div>
  <div className="component">
    <a href="./toastandroid">
      <h3>ToastAndroid</h3>
      <p>Android Toastアラートを作成します。</p>
    </a>
  </div>
</div>

## iOS Components and APIs

 次のコンポーネントの多くは、一般的に使用されるUIKitクラスのラッパーを提供します。

<div className="component-grid component-grid-border">
  <div className="component">
    <a href="./actionsheetios">
      <h3>ActionSheetIOS</h3>
      <p>iOSのアクションシートまたは共有シートを表示するためのAPI。</p>
    </a>
  </div>
</div>

## Others
 
その他これらのコンポーネントは特定の用途に役立つかもしれません。コンポーネントとAPIの完全なリストについては、左側のサイドバー（または画面が狭い場合は上のメニュー）をチェックしてください。

<div className="component-grid">
  <div className="component">
    <a href="./activityindicator">
      <h3>ActivityIndicator</h3>
      <p>円形の荷重インジケータを表示します。</p>
    </a>
  </div>
  <div className="component">
    <a href="./alert">
      <h3>Alert</h3>
      <p>指定されたタイトルとメッセージで警告ダイアログを起動します。</p>
    </a>
  </div>
  <div className="component">
    <a href="./animated">
      <h3>Animated</h3>
      <p>構築と保守が簡単な、流動的でパワフルなアニメーションを作成するためのライブラリです。</p>
    </a>
  </div>
  <div className="component">
    <a href="./dimensions">
      <h3>Dimensions</h3>
      <p>デバイスの寸法を取得するためのインターフェイスを提供します。</p>
    </a>
  </div>
  <div className="component">
    <a href="./keyboardavoidingview">
      <h3>KeyboardAvoidingView</h3>
      <p>仮想キーボードの邪魔にならないように自動的に移動するビューを提供します。</p>
    </a>
  </div>
  <div className="component">
    <a href="./linking">
      <h3>Linking</h3>
      <p>着信アプリリンクと発信アプリリンクの両方を操作するための一般的なインターフェースを提供します。</p>
    </a>
  </div>
  <div className="component">
    <a href="./modal">
      <h3>Modal</h3>
      <p>コンテンツを囲むビューの上に簡単に表示する方法を提供します。</p>
    </a>
  </div>
  <div className="component">
    <a href="./pixelratio">
      <h3>PixelRatio</h3>
      <p>デバイスのピクセル密度へのアクセスを提供します。</p>
    </a>
  </div>
  <div className="component">
    <a href="./refreshcontrol">
      <h3>RefreshControl</h3>
      <p>このコンポーネントは<code>ScrollView</code>内で使用され、Pull To Refresh機能を追加します。</p>
    </a>
  </div>
  <div className="component">
    <a href="./statusbar">
      <h3>StatusBar</h3>
      <p>アプリのステータスバーを制御するコンポーネント。</p>
    </a>
  </div>
</div>
