---
id: performance
title: Performance Overview
---

WebViewベースのツールの代わりにReact Native を使用する説得力のある理由は、毎秒60フレームを実現し、アプリのネイティブなルックアンドフィールを実現するためです。可能な限り、React Nativeが正しいことを行い、パフォーマンスの最適化ではなくアプリに集中できるようにしたいのですが、まだ達成していない分野や、React Native（ネイティブコードを直接記述するのと同様）が最適化の最善の方法を決定できないため、手動による介入が必要になります。デフォルトではバタースムーズなUIパフォーマンスを提供するよう最善を尽くしていますが、それが不可能な場合もあります。

このガイドは、[パフォーマンス問題のトラブルシュート](profiling.md) を理解するうえで役立つ基本事項を教えることと、[一般的な問題の原因とその推奨される解決策](performance.md#common-sources-of-performance-problems) について説明することを目的としています。

## What you need to know about frames

あなたの祖父母の世代が映画を ["動く写真"](https://www.youtube.com/watch?v=F1i40rnpOsA) と呼ぶのには理由があります。ビデオのリアルな動きは、静止画を一定の速度で素早く変化させることによって作り出される錯覚だからです。これらの各画像をフレームと呼びます。1 秒あたりに表示されるフレーム数は、ビデオ（またはユーザーインターフェイス）がどれだけスムーズで、最終的に本物そっくりに見えるかに直接影響します。iOS デバイスでは 1 秒あたり 60 フレームが表示されるため、ユーザーと UI システムは、その間隔でユーザーが画面に表示する静止画像（フレーム）の生成に必要なすべての作業を、約 16.67 ミリ秒で実行できます。割り当てられた 16.67 ミリ秒以内にそのフレームを生成するのに必要な作業を実行できない場合、「フレームをドロップ」して UI が応答しなくなります。

さて、ちょっと混乱させますが、アプリで [Dev Menu](debugging.md#accessing-the-dev-menu) を開いて `Show Perf Monitor` を切り替えてください。フレームレートが 2 種類あることに気付くでしょう。

![](/docs/assets/PerfUtil.png)

### JS frame rate (JavaScript thread)

ほとんどの React Native アプリケーションでは、ビジネスロジックは JavaScript スレッド上で実行されます。そのスレッド上に React アプリケーションが存在し、API 呼び出しが行われ、タッチイベントが処理されます。ネイティブバックビューへの更新は、イベントループの各繰り返しの最後、フレームデッドライン前 (すべてがうまくいけば) にバッチ処理されてネイティブ側に送信されます。JavaScript スレッドがフレームに対して応答しない場合、ドロップされたフレームとみなされます。たとえば、複雑なアプリケーションのルートコンポーネントで `this.setState` を呼び出して、計算量の多いコンポーネントサブツリーを再レンダリングした場合、200 ミリ秒かかり、12 フレームがドロップされると考えられます。その間、JavaScriptで制御されるアニメーションはすべてフリーズしているように見えます。100ミリ秒以上かかるものがあれば、ユーザーはフレームドロップを認識することでしょう。

これは `Navigator` トランジション 中によく起こります。新しいルートをプッシュすると、JavaScript スレッドは適切なコマンドをネイティブ側に送信してバッキングビューを作成するために、シーンに必要なすべてのコンポーネントをレンダリングする必要があります。トランジションは JavaScript スレッドによって制御されるため、ここで行われている作業が数フレームかかって [jank](http://jankfree.org/) が発生するのが一般的です。コンポーネントが `componentDidMount` に対して追加の処理を行うことがあり、その結果、遷移中に 2 回目の処理が途切れることがあります。

別の例としては、タッチへの応答があります。たとえば、JavaScript スレッドの複数のフレームにわたって作業を行っている場合、`TouchableOpacity` への応答が遅れることがあります。これは、JavaScript スレッドがビジー状態で、メインスレッドから送信された生のタッチイベントを処理できないためです。その結果、`TouchableOpacity` はタッチイベントに反応できず、ネイティブビューに不透明度の調整を指示できません。

### UI frame rate (main thread)

`NavigatorIOS` のパフォーマンスが `Navigator` よりもそのままの状態で優れていることに多くの人が気づいています。その理由は、トランジションのアニメーションはすべてメインスレッドで行われるため、JavaScriptスレッドでのフレームドロップによって中断されないためです。

同様に、`ScrollView` はメインスレッドにあるので、JavaScript スレッドがロックアップしているときでも `ScrollView` を上下にスクロールできます。スクロールイベントは JS スレッドに送られますが、スクロールが発生するためには受信する必要はありません。

## Common sources of performance problems

### Running in development mode (`dev=true`)

開発モードで実行すると、JavaScriptスレッドのパフォーマンスが大幅に低下します。これは避けられないことです。PropTypes やその他のさまざまなアサーションの検証など、適切な警告やエラーメッセージを出すには、実行時にさらに多くの作業を行う必要があります。必ず [release builds](running-on-device.md#building-your-app-for-production) でパフォーマンスをテストしてください。

### Using `console.log` statements

バンドルされたアプリを実行する場合、これらのステートメントは JavaScript スレッドに大きなボトルネックを引き起こす可能性があります。これには [redux-logger](https://github.com/evgenyrodionov/redux-logger) などのデバッグライブラリからの呼び出しも含まれるため、バンドルする前に必ず削除してください。この [babel plugin](https://babeljs.io/docs/plugins/transform-remove-console/) を使用して `console.*` 呼び出しをすべて削除することもできます。最初に `npm i babel-plugin-transform-remove-console --save-dev` でインストールし、次にプロジェクトディレクトリの `.babelrc` ファイルを次のように編集する必要があります。

```json
{
  "env": {
    "production": {
      "plugins": ["transform-remove-console"]
    }
  }
}
```

これにより、プロジェクトのリリース (プロダクション) バージョンの `console.*` 呼び出しがすべて自動的に削除されます。

プロジェクトで `console.*` 呼び出しが行われていない場合でも、このプラグインを使用することをお勧めします。サードパーティライブラリから呼び出されることもあるためです。

### `ListView` initial rendering is too slow or scroll performance is bad for large lists

代わりに新しい [`FlatList`](flatlist.md) または [`SectionList`](sectionlist.md) コンポーネントを使用してください。API を簡略化するだけでなく、新しいリストコンポーネントではパフォーマンスが大幅に向上しています。主なものは、行数に関係なくほぼ一定のメモリ使用量です。

[`FlatList`](flatlist.md) のレンダリングが遅い場合は、[`getItemLayout`](flatlist.md#getitemlayout) を実装していることを確認してください。レンダリングされたアイテムの測定をスキップしてレンダリング速度を最適化するためです。

### JS FPS plunges when re-rendering a view that hardly changes

ListView を使用している場合は、`rowHasChanged` 関数を用意する必要があります。これにより、行を再レンダリングする必要があるかどうかをすばやく判断できるため、多くの作業を減らすことができます。不変のデータ構造を使用している場合は、参照の等価性チェックだけで済みます。

同様に、`shouldComponentUpdate` を実装して、コンポーネントを再レンダリングしたい正確な条件を指定できます。純粋コンポーネント (レンダー関数の戻り値はプロパティとstate に完全に依存する) を記述する場合、PureComponent を利用してこれを代行できます。繰り返しになりますが、これを高速に保つには、不変のデータ構造が役立ちます。大量のオブジェクトのリストを詳細に比較する必要がある場合は、コンポーネント全体を再レンダリングする方が速く、必要なコードも少なくて済みます。

### Dropping JS thread FPS because of doing a lot of work on the JavaScript thread at the same time

「ナビゲーターの遷移が遅い」というのが最も一般的な症状ですが、これ以外の場合もあります。InteractionManagerを使用するのも良い方法ですが、ユーザーエクスペリエンスのコストが高すぎてアニメーション中の作業を遅らせることができない場合は、LayoutAnimation を検討することをお勧めします。

Animated API は現在、[`useNativeDriver: true`を設定](/blog/2017/02/14/using-native-driver-for-animated#how-do-i-use-this-in-my-app) を実行しない限り JavaScript スレッドで各キーフレームをオンデマンドで計算しますが、LayoutAnimation はコアアニメーションを活用しており、JS スレッドやメインスレッドのフレームドロップによる影響を受けません。

これを使った例の 1 つは、複数のネットワークリクエストの応答を初期化して受け取ったり、モーダルの内容をレンダリングしたり、モーダルを開いたときのビューを更新したりしながら、モーダルでアニメーションさせる（上から下にスライドして半透明のオーバーレイでフェードする）場合です。LayoutAnimation の使用方法の詳細については、アニメーションガイドを参照してください。

注意事項:

- LayoutAnimation は、ファイアー・アンド・フォーゲット・アニメーション (「静的」アニメーション) でのみ機能します。中断させたい場合は `Animated` を使用する必要があります。

### Moving a view on the screen (scrolling, translating, rotating) drops UI thread FPS

これは、背景が透明なテキストが画像の上に配置されている場合や、フレームごとにビューを再描画するためにアルファ合成が必要な場合に特に当てはまります。`shouldRasterizeIOS` または `renderToHardwareTextureAndroid` を有効にすると、これが非常に役立つことがわかります。

これを使いすぎないように注意してください。使いすぎると、メモリ使用量が急増する可能性があります。これらのプロップを使用する際のパフォーマンスとメモリ使用量をプロファイリングしてください。ビューを今後移動する予定がない場合は、このプロパティをオフにしてください。

### Animating the size of an image drops UI thread FPS

iOS では、画像コンポーネントの幅または高さを調整するたびに、元の画像から再トリミングおよび拡大縮小されます。これは、特に大きな画像の場合、非常に高価になる可能性があります。代わりに、`transform: [{scale}]` スタイルプロパティを使用してサイズをアニメートしてください。その一例として、画像をタップして全画面にズームインする場合があります。

### My TouchableX view isn't very responsive

タッチに反応するコンポーネントの不透明度やハイライトを調整しているのと同じフレームでアクションを実行しても、`onPress` 関数が戻るまでその効果が見られないことがあります。`onPress` が `setState` を実行して、処理量が多くなり、フレーム数が少し落ちるような場合は、この現象が発生する可能性があります。これを解決するには、`onPress` ハンドラー内の任意のアクションを `requestAnimationFrame` でラップします。

```tsx
handleOnPress() {
  requestAnimationFrame(() => {
    this.doExpensiveAction();
  });
}
```

### Slow navigator transitions

前述のように、`Navigator` アニメーションは JavaScript スレッドによって制御されます。"push from right"シーントランジションを想像してみてください。フレームごとに、新しいシーンが右から左に移動し、画面外から（たとえば、Xオフセット320で）開始し、シーンがXオフセット0になると最終的に落ち着きます。この遷移中の各フレームで、JavaScript スレッドは新しい X オフセットをメインスレッドに送信する必要があります。JavaScript スレッドがロックアップされている場合、そのスレッドはロックアップできないため、そのフレームでは更新が行われず、アニメーションが途切れます。

これに対する1つの解決策は、JavaScriptベースのアニメーションをメインスレッドにオフロードできるようにすることです。このアプローチで上記の例と同じことを行うとしたら、トランジションの開始時に新しいシーンのすべての X オフセットのリストを計算し、それらをメインスレッドに送信して最適化された方法で実行することができます。JavaScriptスレッドはこの責任から解放されたので、シーンのレンダリング中に数フレーム落ちても大したことではありません。きれいな遷移に気を取られすぎて、おそらく気付かないでしょう。

これを解決することが、新しい [React Navigation](navigation.md) ライブラリの背後にある主な目標の1つです。React Navigation のビューは、ネイティブコンポーネントと [`Animated`](animated.md) ライブラリを使用して、ネイティブスレッドで実行される 60 FPS アニメーションを配信します。
