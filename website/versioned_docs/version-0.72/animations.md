---
id: animations
title: Animations
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

アニメーションは、優れたユーザーエクスペリエンスを実現するために非常に重要です。静止している物体は、動き始めると慣性に打ち勝たなければなりません。動いている物体には勢いがあり、すぐに止まることはめったにありません。アニメーションを使うと、物理的にリアルな動きをインターフェイスで伝えることができます。

React Native は、特定の値をきめ細かくインタラクティブに制御するための [`Animated`](animations#animated-api) と、アニメーション化されたグローバルレイアウトトランザクション用の [`LayoutAnimation`](animations#layoutanimation-api) という 2 つの補完的なアニメーションシステムを提供します。

## `Animated` API

[`Animated`](animated) API は、多種多様な興味深いアニメーションやインタラクションパターンを非常にパフォーマンスに優れた方法で簡潔に表現するように設計されています。`Animated`は、入力と出力の間の宣言的な関係（間に構成可能な変換を含む）と、時間ベースのアニメーション実行を制御するための`start`/`stop`メソッドに焦点を当てています。

`Animated`は、`View`、`Text`、`Image`、`ScrollView`、`FlatList`、`SectionList` の6つのアニメーション可能なコンポーネントタイプをエクスポートしますが、`Animated.createAnimatedComponent()`を使用して独自のコンポーネントを作成することもできます。

たとえば、マウントするとフェードインするコンテナビューは次のようになります。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer ext=js
import React, {useRef, useEffect} from 'react';
import {Animated, Text, View} from 'react-native';

const FadeInView = props => {
  const fadeAnim = useRef(new Animated.Value(0)).current; // Initial value for opacity: 0

  useEffect(() => {
    Animated.timing(fadeAnim, {
      toValue: 1,
      duration: 10000,
      useNativeDriver: true,
    }).start();
  }, [fadeAnim]);

  return (
    <Animated.View // Special animatable View
      style={{
        ...props.style,
        opacity: fadeAnim, // Bind opacity to animated value
      }}>
      {props.children}
    </Animated.View>
  );
};

// You can then use your `FadeInView` in place of a `View` in your components:
export default () => {
  return (
    <View
      style={{
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
      }}>
      <FadeInView
        style={{
          width: 250,
          height: 50,
          backgroundColor: 'powderblue',
        }}>
        <Text style={{fontSize: 28, textAlign: 'center', margin: 10}}>
          Fading in
        </Text>
      </FadeInView>
    </View>
  );
};
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer ext=tsx
import React, {useRef, useEffect} from 'react';
import {Animated, Text, View} from 'react-native';
import type {PropsWithChildren} from 'react';
import type {ViewStyle} from 'react-native';

type FadeInViewProps = PropsWithChildren<{style: ViewStyle}>;

const FadeInView: React.FC<FadeInViewProps> = props => {
  const fadeAnim = useRef(new Animated.Value(0)).current; // Initial value for opacity: 0

  useEffect(() => {
    Animated.timing(fadeAnim, {
      toValue: 1,
      duration: 10000,
      useNativeDriver: true,
    }).start();
  }, [fadeAnim]);

  return (
    <Animated.View // Special animatable View
      style={{
        ...props.style,
        opacity: fadeAnim, // Bind opacity to animated value
      }}>
      {props.children}
    </Animated.View>
  );
};

// You can then use your `FadeInView` in place of a `View` in your components:
export default () => {
  return (
    <View
      style={{
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
      }}>
      <FadeInView
        style={{
          width: 250,
          height: 50,
          backgroundColor: 'powderblue',
        }}>
        <Text style={{fontSize: 28, textAlign: 'center', margin: 10}}>
          Fading in
        </Text>
      </FadeInView>
    </View>
  );
};
```

</TabItem>
</Tabs>

ここで何が起こっているのかを詳しく見てみましょう。`FadeInView` コンストラクターでは、`fadeAnim`という新しい`Animated.Value`が`state`の一部として初期化されます。`View`の不透明度プロパティは、このアニメーション値にマップされます。バックグラウンドでは、数値が抽出され、不透明度を設定するために使用されます。

コンポーネントがマウントされると、不透明度は 0 に設定されます。次に、`fadeAnim` アニメーション値でイージングアニメーションが開始され、値が最終値の 1 にアニメートされるにつれて、各フレームのすべての従属マッピング (この場合は不透明度のみ) が更新されます。

これは、`setState`を呼び出して再レンダリングするよりも速い最適化された方法で行われます。構成全体が宣言型であるため、構成をシリアル化し、優先順位の高いスレッドでアニメーションを実行する最適化をさらに実装できます。

### Configuring animations

アニメーションは詳細に設定可能です。カスタムおよび事前定義されたイージング関数、ディレイ、持続時間、減衰係数、スプリング定数などはすべて、アニメーションのタイプに応じて微調整できます。

`Animated` にはいくつかのアニメーションタイプがあり、最もよく使われるのは [`Animated.timing()`](animated#timing) です。さまざまな定義済みのイージング関数を使用して、時間の経過とともに値をアニメーション化できます。また、独自のイージング関数を使用することもできます。イージング関数は通常、アニメーションでオブジェクトの段階的な加速と減速を伝えるために使用されます。

デフォルトでは、`timing` は EaseIn-Out カーブを使用します。このカーブは、徐々に加速して最大速度まで伝え、徐々に減速して停止することで終了します。`easing` パラメーターを渡すことで、別のイージング関数を指定できます。カスタム `duration` や、アニメーション開始前の `delay` もサポートされています。

たとえば、最終位置に移動する前に少し後退するオブジェクトの 2 秒間のアニメーションを作成したい場合:

```tsx
Animated.timing(this.state.xPosition, {
  toValue: 100,
  easing: Easing.back(),
  duration: 2000,
  useNativeDriver: true,
}).start();
```

`Animated` API リファレンスの [Configuring animations](animated#configuring-animations) セクションを見て、組み込みアニメーションでサポートされているすべての設定パラメーターについて詳しく学んでください。

### Composing animations

アニメーションは組み合わせて、順番に再生することも、並行して再生することもできます。シーケンシャルアニメーションは、前のアニメーションが終了した直後に再生することも、指定した遅延後に開始することもできます。`Animated` API には `sequence()` や `delay()` などのいくつかのメソッドがあり、それぞれがアニメーションの配列を使用して実行し、必要に応じて自動的に `start()` /`stop()` を呼び出します。

たとえば、次のアニメーションは、惰性で進んで停止し、平行して旋回しながら跳ね返ります。

```tsx
Animated.sequence([
  // decay, then spring to start and twirl
  Animated.decay(position, {
    // coast to a stop
    velocity: {x: gestureState.vx, y: gestureState.vy}, // velocity from gesture release
    deceleration: 0.997,
    useNativeDriver: true,
  }),
  Animated.parallel([
    // after decay, in parallel:
    Animated.spring(position, {
      toValue: {x: 0, y: 0}, // return to start
      useNativeDriver: true,
    }),
    Animated.timing(twirl, {
      // and twirl
      toValue: 360,
      useNativeDriver: true,
    }),
  ]),
]).start(); // start the sequence group
```

1 つのアニメーションが停止または中断されると、グループ内の他のすべてのアニメーションも停止します。`Animated.parallel` には `stopTogether` オプションがあり、`false` に設定するとこれを無効にできます。

コンポジションメソッドの完全なリストは、`Animated` API リファレンスの [Composing animations](animated#composing-animations) セクションにあります。

### Combining animated values

[combine two animated values](animated#combining-animated-values) で加算、乗算、除算、またはモジュロを使って新しいアニメーション値を作成できます。

計算のために、アニメーション化された値が別のアニメーション値を逆にする必要がある場合があります。例としては、スケール (2x --> 0.5x) の反転があります。

```tsx
const a = new Animated.Value(1);
const b = Animated.divide(1, a);

Animated.spring(a, {
  toValue: 2,
  useNativeDriver: true,
}).start();
```

### Interpolation

各プロパティは、最初に補間を実行できます。補間は入力範囲を出力範囲にマッピングします。通常は線形補間を使用しますが、イージング関数もサポートします。デフォルトでは、指定した範囲を超えて曲線を外挿しますが、出力値を固定することもできます。

0 ～ 1 の範囲を 0 ～ 100 の範囲に変換する基本的なマッピングは次のようになります。

```tsx
value.interpolate({
  inputRange: [0, 1],
  outputRange: [0, 100],
});
```

たとえば、`Animated.Value` は 0 から 1 になると考えて、位置は 150 ピクセルから 0px に、不透明度は 0 から 1 にアニメートしたい場合があります。これは、上の例の `style` を以下のように変更することで実現できます。

```tsx
  style={{
    opacity: this.state.fadeAnim, // Binds directly
    transform: [{
      translateY: this.state.fadeAnim.interpolate({
        inputRange: [0, 1],
        outputRange: [150, 0]  // 0 : 150, 0.5 : 75, 1 : 0
      }),
    }],
  }}
```

[`interpolate()`](animated#interpolate) は複数のレンジセグメントにも対応しているので、デッドゾーンの定義やその他の便利なトリックに便利です。たとえば、-300 の否定関係を -100 で 0 になり、0 で 1 に戻り、100 でゼロに戻り、それ以降はすべてデッドゾーンが 0 のままになるようにするには、次のようにします。

```tsx
value.interpolate({
  inputRange: [-300, -100, 0, 100, 101],
  outputRange: [300, 0, 1, 0, 0],
});
```

これは以下のようにマップされます。:

```
Input | Output
------|-------
  -400|    450
  -300|    300
  -200|    150
  -100|      0
   -50|    0.5
     0|      1
    50|    0.5
   100|      0
   101|      0
   200|      0
```

`interpolate()` は文字列へのマッピングもサポートしているため、単位付きの値だけでなく色もアニメートできます。たとえば、回転をアニメートしたい場合は、次のようにします。

```tsx
value.interpolate({
  inputRange: [0, 360],
  outputRange: ['0deg', '360deg'],
});
```

`interpolate()` は任意のイージング関数もサポートしており、その多くは [`Easing`](easing) モジュールですでに実装されています。`interpolate()` には `outputRange` を推定するための設定可能な動作もあります。`extrapolate`、`extrapolateLeft`、または `extrapolateRight` オプションを設定して外挿を設定できます。デフォルト値は `extend` ですが、`clamp` を使用して出力値が `outputRange` を超えないようにすることができます。

### Tracking dynamic values

Animated の値は、アニメーションの `toValue` を単純な数値ではなく別のアニメーション値に設定することで、他の値を追跡することもできます。たとえば、AndroidのMessengerで使用されているような「チャットヘッズ」アニメーションは、別のアニメーション値に `spring()` をピン留めして実装することも、厳密なトラッキングの場合は `timing()` と `duration` を 0 にして実装することもできます。補間を使って合成することもできます。

```tsx
Animated.spring(follower, {toValue: leader}).start();
Animated.timing(opacity, {
  toValue: pan.x.interpolate({
    inputRange: [0, 300],
    outputRange: [1, 0],
    useNativeDriver: true,
  }),
}).start();
```

`leader` と `follower` のアニメーション値は `Animated.ValueXY()` を使用して実装されます。`ValueXY` は、パンニングやドラッグなどの 2D インタラクションを扱うのに便利な方法です。これは、2 つの `Animated.Value` インスタンスとそれらを呼び出すいくつかのヘルパー関数を含む基本的なラッパーなので、多くの場合 `ValueXY` は `Value` の代わりに簡単に使えます。これにより、上の例の x 値と y 値の両方を追跡できます。

### Tracking gestures

[`Animated.event`](animated#event) を使用すると、パンやスクロールなどのジェスチャやその他のイベントをアニメーション値に直接マップできます。これは構造化されたマップ構文で行われるため、複雑なイベントオブジェクトから値を抽出できます。最初のレベルは複数の引数にまたがるマッピングを可能にする配列で、その配列にはネストされたオブジェクトが含まれています。

たとえば、水平スクロールジェスチャを使用する場合、`event.nativeEvent.contentOffset.x` を `scrollX` (an `Animated.Value`) にマップするには次のようにします。

```tsx
 onScroll={Animated.event(
   // scrollX = e.nativeEvent.contentOffset.x
   [{nativeEvent: {
        contentOffset: {
          x: scrollX
        }
      }
    }]
 )}
```

次の例では、`ScrollView` で使用されている `Animated.event` を使用してスクロール位置インジケーターをアニメーション化する水平スクロールカルーセルを実装しています。

#### ScrollView with Animated Event Example

```SnackPlayer name=Animated&supportedPlatforms=ios,android
import React, {useRef} from 'react';
import {
  SafeAreaView,
  ScrollView,
  Text,
  StyleSheet,
  View,
  ImageBackground,
  Animated,
  useWindowDimensions,
} from 'react-native';

const images = new Array(6).fill(
  'https://images.unsplash.com/photo-1556740749-887f6717d7e4',
);

const App = () => {
  const scrollX = useRef(new Animated.Value(0)).current;

  const {width: windowWidth} = useWindowDimensions();

  return (
    <SafeAreaView style={styles.container}>
      <View style={styles.scrollContainer}>
        <ScrollView
          horizontal={true}
          pagingEnabled
          showsHorizontalScrollIndicator={false}
          onScroll={Animated.event([
            {
              nativeEvent: {
                contentOffset: {
                  x: scrollX,
                },
              },
            },
          ])}
          scrollEventThrottle={1}>
          {images.map((image, imageIndex) => {
            return (
              <View style={{width: windowWidth, height: 250}} key={imageIndex}>
                <ImageBackground source={{uri: image}} style={styles.card}>
                  <View style={styles.textContainer}>
                    <Text style={styles.infoText}>
                      {'Image - ' + imageIndex}
                    </Text>
                  </View>
                </ImageBackground>
              </View>
            );
          })}
        </ScrollView>
        <View style={styles.indicatorContainer}>
          {images.map((image, imageIndex) => {
            const width = scrollX.interpolate({
              inputRange: [
                windowWidth * (imageIndex - 1),
                windowWidth * imageIndex,
                windowWidth * (imageIndex + 1),
              ],
              outputRange: [8, 16, 8],
              extrapolate: 'clamp',
            });
            return (
              <Animated.View
                key={imageIndex}
                style={[styles.normalDot, {width}]}
              />
            );
          })}
        </View>
      </View>
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  scrollContainer: {
    height: 300,
    alignItems: 'center',
    justifyContent: 'center',
  },
  card: {
    flex: 1,
    marginVertical: 4,
    marginHorizontal: 16,
    borderRadius: 5,
    overflow: 'hidden',
    alignItems: 'center',
    justifyContent: 'center',
  },
  textContainer: {
    backgroundColor: 'rgba(0,0,0, 0.7)',
    paddingHorizontal: 24,
    paddingVertical: 8,
    borderRadius: 5,
  },
  infoText: {
    color: 'white',
    fontSize: 16,
    fontWeight: 'bold',
  },
  normalDot: {
    height: 8,
    width: 8,
    borderRadius: 4,
    backgroundColor: 'silver',
    marginHorizontal: 4,
  },
  indicatorContainer: {
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'center',
  },
});

export default App;
```

`PanResponder` を使用する場合、次のコードを使用して `gestureState.dx` と `gestureState.dy` から x 位置と y 位置を抽出できます。`PanResponder` ハンドラーに渡される 2 番目の引数 `gestureState` だけが対象なので、配列の最初の位置には `null` を使用します。

```tsx
onPanResponderMove={Animated.event(
  [null, // ignore the native event
  // extract dx and dy from gestureState
  // like 'pan.x = gestureState.dx, pan.y = gestureState.dy'
  {dx: pan.x, dy: pan.y}
])}
```

#### PanResponder with Animated Event Example

```SnackPlayer name=Animated
import React, {useRef} from 'react';
import {Animated, View, StyleSheet, PanResponder, Text} from 'react-native';

const App = () => {
  const pan = useRef(new Animated.ValueXY()).current;
  const panResponder = useRef(
    PanResponder.create({
      onMoveShouldSetPanResponder: () => true,
      onPanResponderMove: Animated.event([null, {dx: pan.x, dy: pan.y}]),
      onPanResponderRelease: () => {
        Animated.spring(pan, {
          toValue: {x: 0, y: 0},
          useNativeDriver: true,
        }).start();
      },
    }),
  ).current;

  return (
    <View style={styles.container}>
      <Text style={styles.titleText}>Drag & Release this box!</Text>
      <Animated.View
        style={{
          transform: [{translateX: pan.x}, {translateY: pan.y}],
        }}
        {...panResponder.panHandlers}>
        <View style={styles.box} />
      </Animated.View>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  titleText: {
    fontSize: 14,
    lineHeight: 24,
    fontWeight: 'bold',
  },
  box: {
    height: 150,
    width: 150,
    backgroundColor: 'blue',
    borderRadius: 5,
  },
});

export default App;
```

### Responding to the current animation value

アニメーション中に現在の値を読み取る明確な方法がないことに気付くかもしれません。これは、最適化により、値がネイティブランタイムでのみ認識される可能性があるためです。現在の値に応じて JavaScript を実行する必要がある場合は、次の 2 つの方法があります。

- `spring.stopAnimation(callback)` はアニメーションを停止し、最終値で `callback` を呼び出します。これはジェスチャートランジションを行うときに便利です。
- `spring.addListener(callback)` はアニメーションの実行中に `callback` を非同期で呼び出し、最新の値を提供します。これは、ユーザーがボブルを近づけるとボブルを新しいオプションにスナップするなどのstate 変化をトリガーする場合に便利です。このような大きなstate 変化は、60 fps で実行する必要があるパンニングなどの連続したジェスチャーと比較して、数フレームのラグの影響を受けにくいためです。

`Animated` は完全にシリアル化できるように設計されているため、通常の JavaScript イベントループとは関係なく、アニメーションを高パフォーマンスで実行できます。これはAPIに影響するので、完全同期システムと比べて何かをするのが少し難しいと思われる場合は、そのことを覚えておいてください。これらの制限のいくつかを回避する方法として `Animated.Value.addListener` をチェックしてください。ただし、将来的にはパフォーマンスに影響する可能性があるため、使用は控えてください。

### Using the native driver

`Animated` API はシリアル化できるように設計されています。[native driver](/blog/2017/02/14/using-native-driver-for-animated) を使用することで、アニメーションを開始する前にアニメーションに関するすべてをネイティブに送信し、ネイティブコードがすべてのフレームでブリッジを経由することなく UI スレッドでアニメーションを実行できるようになります。アニメーションが開始されると、アニメーションに影響を与えずに JS スレッドをブロックできます。

通常のアニメーションにネイティブドライバを使用するには、起動時にアニメーション設定で `useNativeDriver: true` を設定します。`useNativeDriver` プロパティのないアニメーションは、従来の理由でデフォルトで false になりますが、警告 (および TypeScript ではタイプチェックエラー) が表示されます。

```tsx
Animated.timing(this.state.animatedValue, {
  toValue: 1,
  duration: 500,
  useNativeDriver: true, // <-- Set this to true
}).start();
```

Animated 値は 1 つのドライバのみと互換性があるため、値でアニメーションを開始するときにネイティブドライバを使用する場合は、その値のすべてのアニメーションもネイティブドライバを使用するようにしてください。

ネイティブドライバは `Animated.event` でも動作します。これは、スクロール位置に従うアニメーションに特に役立ちます。ネイティブドライバーがないと、React Nativeの非同期性により、アニメーションは常にジェスチャーの後ろのフレームで実行されます。

```tsx
<Animated.ScrollView // <-- Use the Animated ScrollView wrapper
  scrollEventThrottle={1} // <-- Use 1 here to make sure no events are ever missed
  onScroll={Animated.event(
    [
      {
        nativeEvent: {
          contentOffset: {y: this.state.animatedValue},
        },
      },
    ],
    {useNativeDriver: true}, // <-- Set this to true
  )}>
  {content}
</Animated.ScrollView>
```

[RNTester app](https://github.com/facebook/react-native/blob/main/packages/rn-tester/) を実行してからネイティブAnimated サンプルを読み込むと、ネイティブドライバーの動作を確認できます。[source code](https://github.com/facebook/react-native/blob/master/packages/rn-tester/js/examples/NativeAnimation/NativeAnimationsExample.js) を見て、これらの例がどのように作成されたかを知ることもできます。

#### Caveats

現在、`Animated` でできることのすべてがネイティブドライバーでサポートされているわけではありません。主な制限は、アニメーション化できるのはレイアウト以外のプロパティのみであるということです。`transform` や `opacity` などは動作しますが、Flexbox や位置プロパティは動作しません。`Animated.event` を使用する場合、ダイレクトイベントでのみ機能し、バブリングイベントでは機能しません。つまり、`PanResponder` では動作しませんが `ScrollView#onScroll` などでは動作します。

アニメーションの実行中に、`VirtualizedList` コンポーネントがより多くの行をレンダリングできなくなる可能性があります。ユーザーがリストをスクロールしているときに長いアニメーションやループするアニメーションを実行する必要がある場合は、アニメーションの設定で `isInteraction: false` を使用するとこの問題を防ぐことができます。

### Bear in mind

`rotateY`、`rotateX` などのトランスフォームスタイルを使用する場合は、トランスフォームスタイル `perspective` が適切であることを確認してください。現時点では、一部のアニメーションは、これがないと Android でレンダリングされない場合があります。以下の例をご覧ください。

```tsx
<Animated.View
  style={{
    transform: [
      {scale: this.state.scale},
      {rotateY: this.state.rotateY},
      {perspective: 1000}, // without this line this Animation will not render on Android while working fine on iOS
    ],
  }}
/>
```

### Additional examples

RNTester アプリには、さまざまな `Animated` の使用例があります。

- [AnimatedGratuitousApp](https://github.com/facebook/react-native/tree/main/packages/rn-tester/js/examples/AnimatedGratuitousApp)
- [NativeAnimationsExample](https://github.com/facebook/react-native/blob/main/packages/rn-tester/js/examples/NativeAnimation/NativeAnimationsExample.js)

## `LayoutAnimation` API

`LayoutAnimation` では、次のレンダリング/レイアウトサイクルですべてのビューに使用される `create` アニメーションと `update` アニメーションをグローバルに設定できます。これは、特定のプロパティを直接アニメーション化するためにわざわざ測定または計算せずにFlexbox レイアウトを更新する場合に役立ちます。また、レイアウトの変更が祖先に影響する可能性がある場合に特に役立ちます。たとえば、「もっと見る」拡張で親のサイズが大きくなり、下の行を押し下げると、すべてを同期してアニメーション化するためにコンポーネント間の明示的な調整が必要になります。

`LayoutAnimation` は非常に強力で非常に便利ですが、`Animated` や他のアニメーションライブラリに比べて制御がはるかに少ないため、`LayoutAnimation` に思いどおりの動作をさせることができない場合は、別の方法を使用する必要があるかもしれません。

**Android**でこれを動作させるには、`UIManager` で次のフラグを設定する必要があることに注意してください。

```tsx
UIManager.setLayoutAnimationEnabledExperimental(true);
```

```SnackPlayer name=LayoutAnimations&supportedPlatforms=ios,android
import React from 'react';
import {
  NativeModules,
  LayoutAnimation,
  Text,
  TouchableOpacity,
  StyleSheet,
  View,
} from 'react-native';

const {UIManager} = NativeModules;

UIManager.setLayoutAnimationEnabledExperimental &&
  UIManager.setLayoutAnimationEnabledExperimental(true);

export default class App extends React.Component {
  state = {
    w: 100,
    h: 100,
  };

  _onPress = () => {
    // Animate the update
    LayoutAnimation.spring();
    this.setState({w: this.state.w + 15, h: this.state.h + 15});
  };

  render() {
    return (
      <View style={styles.container}>
        <View
          style={[styles.box, {width: this.state.w, height: this.state.h}]}
        />
        <TouchableOpacity onPress={this._onPress}>
          <View style={styles.button}>
            <Text style={styles.buttonText}>Press me!</Text>
          </View>
        </TouchableOpacity>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  box: {
    width: 200,
    height: 200,
    backgroundColor: 'red',
  },
  button: {
    backgroundColor: 'black',
    paddingHorizontal: 20,
    paddingVertical: 15,
    marginTop: 15,
  },
  buttonText: {
    color: '#fff',
    fontWeight: 'bold',
  },
});
```

この例ではプリセット値を使用しており、必要に応じてアニメーションをカスタマイズできます。詳細は [LayoutAnimation.js](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/LayoutAnimation/LayoutAnimation.js) を参照してください。

## Additional notes

### `requestAnimationFrame`

`requestAnimationFrame` は、おなじみのブラウザで表示されるポリフィルです。関数を唯一の引数として受け入れ、次の再ペイントの前にその関数を呼び出します。すべてのJavaScriptベースのアニメーションAPIの基礎となるアニメーションの必須ビルディングブロックです。一般的には、これを自分で呼び出す必要はありません。アニメーション API がフレーム更新を管理してくれます。

### `setNativeProps`

[in the Direct Manipulation section](direct-manipulation)で言及されている通り, `setNativeProps` を使うと、`setState` を実行してコンポーネント階層を再レンダリングしなくても、ネイティブ・バック・コンポーネント (コンポジット・コンポーネントとは異なり、実際にはネイティブ・ビューにバックアップされているコンポーネント) のプロパティを直接変更できます。

リバウンドの例でこれを使用してスケールを更新できます。これは、更新するコンポーネントが深くネストされていて、`shouldComponentUpdate` で最適化されていない場合に役立つ可能性があります。

アニメーションのフレーム数が落ちる (実行速度が 60 フレーム/秒未満) 場合は、`setNativeProps` または `shouldComponentUpdate` を使用して最適化することを検討してください。または、JavaScript スレッド [with the useNativeDriver option](/blog/2017/02/14/using-native-driver-for-animated) ではなく UI スレッドでアニメーションを実行することもできます。また、[InteractionManager](interactionmanager) を使用して、計算量の多い作業をアニメーションが完了するまで延期することもできます。アプリ内開発メニュー「FPS Monitor」ツールを使用してフレームレートを監視できます。
