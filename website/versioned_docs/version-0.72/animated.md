---
id: animated
title: Animated
---

`Animated`ライブラリは、アニメーションを滑らかでパワフルで、簡単に構築したり管理したりできるように設計されています。`Animated`は、入力と出力の間の宣言的な関係、その間の設定可能な変換、および時間ベースのアニメーション実行を制御するための`start`/`stop`メソッドに焦点を当てています。

アニメーションを作成するための中核となるワークフローは、`Animated.Value`を作成し、それをアニメーション化されるコンポーネントの1つ以上のスタイル属性に接続し、`Animated.timing()`を使用したアニメーションを介して更新を行うことです。

> アニメーション値を直接変更しないでください。[`useRef` フック](https://reactjs.org/docs/hooks-reference.html#useref) を使って可変の参照オブジェクトを返すことができます。この ref オブジェクトの `current` プロパティは、指定された引数として初期化され、コンポーネントのライフサイクル全体を通して持続します。

## Example

次の例には、アニメーション値 `fadeAnim` に基づいてフェードインおよびフェードアウトする `View` が含まれています

```SnackPlayer name=Animated
import React, {useRef} from 'react';
import {
  Animated,
  Text,
  View,
  StyleSheet,
  Button,
  SafeAreaView,
} from 'react-native';

const App = () => {
  // fadeAnim will be used as the value for opacity. Initial Value: 0
  const fadeAnim = useRef(new Animated.Value(0)).current;

  const fadeIn = () => {
    // Will change fadeAnim value to 1 in 5 seconds
    Animated.timing(fadeAnim, {
      toValue: 1,
      duration: 5000,
      useNativeDriver: true,
    }).start();
  };

  const fadeOut = () => {
    // Will change fadeAnim value to 0 in 3 seconds
    Animated.timing(fadeAnim, {
      toValue: 0,
      duration: 3000,
      useNativeDriver: true,
    }).start();
  };

  return (
    <SafeAreaView style={styles.container}>
      <Animated.View
        style={[
          styles.fadingContainer,
          {
            // Bind opacity to animated value
            opacity: fadeAnim,
          },
        ]}>
        <Text style={styles.fadingText}>Fading View!</Text>
      </Animated.View>
      <View style={styles.buttonRow}>
        <Button title="Fade In View" onPress={fadeIn} />
        <Button title="Fade Out View" onPress={fadeOut} />
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
  fadingContainer: {
    padding: 20,
    backgroundColor: 'powderblue',
  },
  fadingText: {
    fontSize: 28,
  },
  buttonRow: {
    flexBasis: 100,
    justifyContent: 'space-evenly',
    marginVertical: 16,
  },
});

export default App;
```

[Animations](animations#animated-api) ガイドを参照して、`Animated` の動作例をさらに確認してください。

## Overview

`Animated` で使用できる値のタイプは 2 つあります。

- [`Animated.Value()`](animated#value) 単一の値用
- [`Animated.ValueXY()`](animated#valuexy) ベクトル用

`Animated.Value` はスタイルプロパティや他のプロップにバインドでき、補間することもできます。単一の `Animated.Value` で任意の数のプロパティを操作できます。

### Configuring animations

`Animated`には3種類のアニメーションタイプがあります。それぞれのアニメーションタイプには、初期値から最終値までの値のアニメート方法を制御する特定のアニメーションカーブがあります。

- [`Animated.decay()`](animated#decay) 初速度で始まり、徐々に減速して停止します。
- [`Animated.spring()`](animated#spring) 基本的なばね物理モデルを提供します。
- [`Animated.timing()`](animated#timing) [easing functions](easing) を使用して、時間の経過とともに値をアニメーション化します。

ほとんどの場合、`timing()`を使うことになるでしょう。デフォルトでは、対称的な easeInOut カーブを使用して、オブジェクトを段階的に加速して最大速度まで到達させ、最後は徐々に減速して停止します。

### Working with animations

アニメーションは、アニメーションで `start()`を呼び出すことで開始されます。`start()`は、アニメーションが終了したときに呼び出される完了コールバックを受け取ります。アニメーションが正常に終了すると、`{finished: true}` で完了コールバックが呼び出されます。終了する前に `stop()`が呼び出されたためにアニメーションが終了した場合 (例えば、ジェスチャーや他のアニメーションによって中断されたため)、`{finished: false}`を受け取ります。

```tsx
Animated.timing({}).start(({finished}) => {
  /* completion callback */
});
```

### Using the native driver

ネイティブドライバーを使用することで、アニメーションを開始する前にアニメーションに関するすべてをネイティブに送信し、ネイティブコードがすべてのフレームでブリッジを経由せずにUIスレッドでアニメーションを実行できるようにします。アニメーションが開始されると、アニメーションに影響を与えずに JS スレッドをブロックできます。

アニメーション設定で `useNativeDriver: true` を指定することで、ネイティブドライバーを使用できます。詳細については、[Animations](animations#using-the-native-driver) ガイドを参照してください。

### Animatable components

アニメートできるのは、アニメート可能なコンポーネントだけです。これらのユニークなコンポーネントは、アニメーション化された値をプロパティにバインドするという魔法のような働きをし、各フレームでのReactのレンダリングとリコンシリエーションプロセスのコストを回避するために、ターゲットを絞ったネイティブアップデートを行います。また、アンマウント時のクリーンアップも処理するので、デフォルトでは安全です。

- [`createAnimatedComponent()`](animated#createanimatedcomponent) はコンポーネントをアニメート可能にするために使用できます。

`Animated`は、上記のラッパーを使用して以下のアニメーション可能なコンポーネントをエクスポートします:

- `Animated.Image`
- `Animated.ScrollView`
- `Animated.Text`
- `Animated.View`
- `Animated.FlatList`
- `Animated.SectionList`

### Composing animations

アニメーションは、コンポジション関数を使用して複雑な方法で組み合わせることもできます。

- [`Animated.delay()`](animated#delay) は、指定した遅延後にアニメーションを開始します。
- [`Animated.parallel()`](animated#parallel) は、同時に複数のアニメーションを開始します。
- [`Animated.sequence()`](animated#sequence) は、アニメーションを順番に開始し、それぞれが完了するのを待ってから次のアニメーションを開始します。
- [`Animated.stagger()`](animated#stagger) は、アニメーションを順番に並行して開始しますが、連続した遅延が発生します。

あるアニメーションの`toValue`を別の`Animated.Value`に設定することで、アニメーションをつなぎ合わせることもできます。アニメーションガイドの [Tracking dynamic values](animations#tracking-dynamic-values) を参照してください。

デフォルトでは、1 つのアニメーションが停止または中断されると、グループ内の他のすべてのアニメーションも停止します。

### Combining animated values

加算、減算、乗算、除算、または剰余演算を使用して 2 つのアニメーション値を組み合わせて、新しいアニメーション値を作成できます。

- [`Animated.add()`](animated#add)
- [`Animated.subtract()`](animated#subtract)
- [`Animated.divide()`](animated#divide)
- [`Animated.modulo()`](animated#modulo)
- [`Animated.multiply()`](animated#multiply)

### Interpolation

`interpolate()`関数を使用すると、入力範囲をさまざまな出力範囲にマップできます。デフォルトでは、指定した範囲を超えて曲線を外挿しますが、出力値を固定することもできます。デフォルトでは線形補間を使用しますが、イージング関数もサポートしています。

- [`interpolate()`](animated#interpolate)

補間について詳しくは、[Animation](animations#interpolation) ガイドをご覧ください。

### Handling gestures and other events

パンニングやスクロールなどのジェスチャやその他のイベントは、`Animated.Event()`を使用してアニメーション値に直接マップできます。これは構造化されたマップ構文で行われるため、複雑なイベントオブジェクトから値を抽出できます。最初のレベルは複数の引数にまたがるマッピングを可能にする配列で、その配列にはネストされたオブジェクトが含まれています。

- [`Animated.event()`](animated#event)

たとえば、水平スクロールジェスチャを使用する場合、`event.nativeEvent.contentOffset.x` を `scrollX` (`Animated.Value`)にマップするには次の操作を行います。:

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

---

# Reference

## Methods

指定された値が value ではなく ValueXy の場合、各設定オプションはスカラーではなく `{x: ..., y: ...}` 形式のベクトルでもかまいません。

### `decay()`

```tsx
static decay(value, config): CompositeAnimation;
```

減衰係数に基づいて初期速度からゼロまでの値をアニメートします。

config は、以下のオプションを持つオブジェクトです。

- `velocity`: 初期速度。必須。
- `deceleration`: 減衰率。デフォルトは 0.997 です。
- `isInteraction`: このアニメーションが `InteractionManager`に「インタラクションハンドル」を作成するかどうか。デフォルトは true です。
- `useNativeDriver`: true の場合はネイティブドライバを使用します。必須。

---

### `timing()`

```tsx
static timing(value, config): CompositeAnimation;
```

時限イージングカーブに沿って値をアニメーション化します。[`Easing`](easing) モジュールにはあらかじめ定義されたカーブがたくさんありますが、独自の関数を使うこともできます。

config は、以下のオプションを持つオブジェクトです。

- `duration`: アニメーションの長さ (ミリ秒)。デフォルトは 500 です。
- `easing`: カーブを定義するイージング機能。デフォルトは `Easing.inOut(Easing.ease)`です。
- `delay`: 遅延 (ミリ秒) 後にアニメーションを開始します。デフォルト 0。
- `isInteraction`: このアニメーションが `InteractionManager`に「インタラクションハンドル」を作成するかどうか。デフォルトは true です。
- `useNativeDriver`: true の場合はネイティブドライバを使用します。必須。

---

### `spring()`

```tsx
static spring(value, config): CompositeAnimation;
```

[減衰調和振動](https://en.wikipedia.org/wiki/Harmonic_oscillator#Damped_harmonic_oscillator) に基づく解析ばねモデルに従って値をアニメーション化します。`ToValue` の更新に合わせて速度のstate を追跡して流動的な動きを作り出し、連鎖させることもできます。

config は、以下のオプションを持つオブジェクトです。

定義できるのはbounciness/speed、tension/friction、もしくは stiffness/damping/massのいずれか1つだけで、1つしか定義できないことに注意してください。

friction/tension または bounciness/speedのオプションは、[`Facebook Pop`](https://github.com/facebook/pop)、[リバウンド](https://github.com/facebookarchive/rebound)、[Origami](http://origami.design/) のスプリングモデルと一致します。

- `friction`: bounciness/overshoot を制御します。デフォルト 7。
- `tension`: speed を制御します。デフォルト 40。
- `speed`: アニメーションの速度を制御します。デフォルト 12。
- `bounciness`: 弾力を制御します。デフォルト 8。

Specifying stiffness/damping/mass as parameters makes `Animated.spring` use an analytical spring model based on the motion equations of a [damped harmonic oscillator](https://en.wikipedia.org/wiki/Harmonic_oscillator#Damped_harmonic_oscillator). This behavior is slightly more precise and faithful to the physics behind spring dynamics, and closely mimics the implementation in iOS's CASpringAnimation.

- `stiffness`: The spring stiffness coefficient. Default 100.
- `damping`: Defines how the spring’s motion should be damped due to the forces of friction. Default 10.
- `mass`: The mass of the object attached to the end of the spring. Default 1.

Other configuration options are as follows:

- `velocity`: The initial velocity of the object attached to the spring. Default 0 (object is at rest).
- `overshootClamping`: Boolean indicating whether the spring should be clamped and not bounce. Default false.
- `restDisplacementThreshold`: The threshold of displacement from rest below which the spring should be considered at rest. Default 0.001.
- `restSpeedThreshold`: The speed at which the spring should be considered at rest in pixels per second. Default 0.001.
- `delay`: Start the animation after delay (milliseconds). Default 0.
- `isInteraction`: Whether or not this animation creates an "interaction handle" on the `InteractionManager`. Default true.
- `useNativeDriver`: Uses the native driver when true. Required.

---

### `add()`

```tsx
static add(a: Animated, b: Animated): AnimatedAddition;
```

Creates a new Animated value composed from two Animated values added together.

---

### `subtract()`

```tsx
static subtract(a: Animated, b: Animated): AnimatedSubtraction;
```

Creates a new Animated value composed by subtracting the second Animated value from the first Animated value.

---

### `divide()`

```tsx
static divide(a: Animated, b: Animated): AnimatedDivision;
```

Creates a new Animated value composed by dividing the first Animated value by the second Animated value.

---

### `multiply()`

```tsx
static multiply(a: Animated, b: Animated): AnimatedMultiplication;
```

Creates a new Animated value composed from two Animated values multiplied together.

---

### `modulo()`

```tsx
static modulo(a: Animated, modulus: number): AnimatedModulo;
```

Creates a new Animated value that is the (non-negative) modulo of the provided Animated value

---

### `diffClamp()`

```tsx
static diffClamp(a: Animated, min: number, max: number): AnimatedDiffClamp;
```

Create a new Animated value that is limited between 2 values. It uses the difference between the last value so even if the value is far from the bounds it will start changing when the value starts getting closer again. (`value = clamp(value + diff, min, max)`).

This is useful with scroll events, for example, to show the navbar when scrolling up and to hide it when scrolling down.

---

### `delay()`

```tsx
static delay(time: number): CompositeAnimation;
```

Starts an animation after the given delay.

---

### `sequence()`

```tsx
static sequence(animations: CompositeAnimation[]): CompositeAnimation;
```

Starts an array of animations in order, waiting for each to complete before starting the next. If the current running animation is stopped, no following animations will be started.

---

### `parallel()`

```tsx
static parallel(
  animations: CompositeAnimation[],
  config?: ParallelConfig
): CompositeAnimation;
```

Starts an array of animations all at the same time. By default, if one of the animations is stopped, they will all be stopped. You can override this with the `stopTogether` flag.

---

### `stagger()`

```tsx
static stagger(
  time: number,
  animations: CompositeAnimation[]
): CompositeAnimation;
```

Array of animations may run in parallel (overlap), but are started in sequence with successive delays. Nice for doing trailing effects.

---

### `loop()`

```tsx
static loop(
  animation: CompositeAnimation[],
  config?: LoopAnimationConfig
): CompositeAnimation;
```

Loops a given animation continuously, so that each time it reaches the end, it resets and begins again from the start. Will loop without blocking the JS thread if the child animation is set to `useNativeDriver: true`. In addition, loops can prevent `VirtualizedList`-based components from rendering more rows while the animation is running. You can pass `isInteraction: false` in the child animation config to fix this.

Config is an object that may have the following options:

- `iterations`: Number of times the animation should loop. Default `-1` (infinite).

---

### `event()`

```tsx
static event(
  argMapping: Mapping[],
  config?: EventConfig
): (...args: any[]) => void;
```

Takes an array of mappings and extracts values from each arg accordingly, then calls `setValue` on the mapped outputs. e.g.

```tsx
onScroll={Animated.event(
  [{nativeEvent: {contentOffset: {x: this._scrollX}}}],
  {listener: (event: ScrollEvent) => console.log(event)}, // Optional async listener
)}
 ...
onPanResponderMove: Animated.event(
  [
    null, // raw event arg ignored
    {dx: this._panX},
  ], // gestureState arg
  {
    listener: (
      event: GestureResponderEvent,
      gestureState: PanResponderGestureState
    ) => console.log(event, gestureState),
  } // Optional async listener
);
```

Config is an object that may have the following options:

- `listener`: Optional async listener.
- `useNativeDriver`: Uses the native driver when true. Required.

---

### `forkEvent()`

```jsx
static forkEvent(event: AnimatedEvent, listener: Function): AnimatedEvent;
```

Advanced imperative API for snooping on animated events that are passed in through props. It permits to add a new javascript listener to an existing `AnimatedEvent`. If `animatedEvent` is a javascript listener, it will merge the 2 listeners into a single one, and if `animatedEvent` is null/undefined, it will assign the javascript listener directly. Use values directly where possible.

---

### `unforkEvent()`

```jsx
static unforkEvent(event: AnimatedEvent, listener: Function);
```

---

### `start()`

```tsx
static start(callback?: (result: {finished: boolean}) => void);
```

Animations are started by calling start() on your animation. start() takes a completion callback that will be called when the animation is done or when the animation is done because stop() was called on it before it could finish.

**Parameters:**

| Name     | Type                                    | Required | Description                                                                                                                                                     |
| -------- | --------------------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| callback | `(result: {finished: boolean}) => void` | No       | Function that will be called after the animation finished running normally or when the animation is done because stop() was called on it before it could finish |

Start example with callback:

```tsx
Animated.timing({}).start(({finished}) => {
  /* completion callback */
});
```

---

### `stop()`

```tsx
static stop();
```

Stops any running animation.

---

### `reset()`

```tsx
static reset();
```

Stops any running animation and resets the value to its original.

## Properties

### `Value`

Standard value class for driving animations. Typically initialized with `new Animated.Value(0);`

You can read more about `Animated.Value` API on the separate [page](animatedvalue).

---

### `ValueXY`

2D value class for driving 2D animations, such as pan gestures.

You can read more about `Animated.ValueXY` API on the separate [page](animatedvaluexy).

---

### `Interpolation`

Exported to use the Interpolation type in flow.

---

### `Node`

Exported for ease of type checking. All animated values derive from this class.

---

### `createAnimatedComponent`

Make any React component Animatable. Used to create `Animated.View`, etc.

---

### `attachNativeEvent`

Imperative API to attach an animated value to an event on a view. Prefer using `Animated.event` with `useNativeDriver: true` if possible.
