---
id: intro-react
title: React Fundamentals
description: To understand React Native fully, you need a solid foundation in React. This short introduction to React can help you get started or get refreshed.
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

React Nativeは、JavaScriptでユーザーインターフェイスを構築するための人気のあるオープンソースライブラリである [React](https://react.dev/) で動作します。React Nativeを最大限に活用するには、React自体を理解することが役に立ちます。このセクションは、入門コースとして使用することも、復習コースとして使用することもできます。

Reactの背後にあるコアコンセプトについて説明します。

- components
- JSX
- props
- state

もっと深く掘り下げたい場合は、[Reactの公式ドキュメント](https://ja.react.dev/learn) を確認することをお勧めします。

## Your first component

このReact入門の残りの部分では、例として猫を使用しています。フレンドリーで親しみやすい生き物で、名前が必要で、仕事にはカフェが必要です。これがあなたの最初のCatコンポーネントです：

```SnackPlayer name=Your%20Cat
import React from 'react';
import {Text} from 'react-native';

const Cat = () => {
  return <Text>Hello, I am your cat!</Text>;
};

export default Cat;
```

その方法は次のとおりです。`Cat`コンポーネントを定義するには、まずJavaScriptの [`import`] (https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) を使ってReactをインポートし、React Nativeの [`Text`](/docs/next/text) コアコンポーネントをインポートしてください。

```tsx
import React from 'react';
import {Text} from 'react-native';
```

コンポーネントは関数として始まります:

```tsx
const Cat = () => {};
```

コンポーネントは設計図と考えることができます。関数コンポーネントが返すものはすべて、**React要素としてレンダリングされます。React要素では、画面に表示したいものを記述できます。

ここで、`Cat`コンポーネントは `<Text>`要素をレンダリングします：

```tsx
const Cat = () => {
  return <Text>Hello, I am your cat!</Text>;
};
```

JavaScriptの [`export default`] (https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export) を使用して関数コンポーネントをエクスポートして、次のようにアプリ全体で使用できます。

```tsx
const Cat = () => {
  return <Text>Hello, I am your cat!</Text>;
};

export default Cat;
```

> これは、コンポーネントをエクスポートする多くの方法の1つです。この種のエクスポートは、Snack Player でうまく機能します。ただし、アプリのファイル構造によっては、別の規則を使用する必要があるかもしれません。この [JavaScriptのインポートとエクスポートに関する便利なチートシート]（https://medium.com/dailyjs/javascript-module-cheatsheet-7bd474f1d829）が役に立ちます。

それでは、その `return`ステートメントを詳しく見てみましょう。`<Text>こんにちは、私はあなたの猫です！</Text>`は、要素を書くのを便利にする一種のJavaScriptシンタックス、JSXを使用しています。

## JSX

ReactとReact Nativeは、**JSX、**という構文を使用して、JavaScriptの内部に次のように要素を書くことができます。`<Text>こんにちは、私はあなたの猫です</Text>！`。Reactのドキュメントには [JSXの総合ガイド] (https://react.dev/learn/writing-markup-with-jsx) がありますので、参照してさらに詳しく学ぶことができます。JSXはJavaScriptなので、その中で変数を使うことができます。`<Text>`ここでは、猫の名前`name`を宣言し、それを `` の中に中括弧で埋め込んでいます。

```SnackPlayer name=Curly%20Braces
import React from 'react';
import {Text} from 'react-native';

const Cat = () => {
  const name = 'Maru';
  return <Text>Hello, I am {name}!</Text>;
};

export default Cat;
```

`{getFullName (「Rum」,「Tum」,「Tugger」)}` のような関数呼び出しを含め、どのJavaScript式も中括弧で囲んで使用できます。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Curly%20Braces&ext=js
import React from 'react';
import {Text} from 'react-native';

const getFullName = (firstName, secondName, thirdName) => {
  return firstName + ' ' + secondName + ' ' + thirdName;
};

const Cat = () => {
  return <Text>Hello, I am {getFullName('Rum', 'Tum', 'Tugger')}!</Text>;
};

export default Cat;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Curly%20Braces&ext=tsx
import React from 'react';
import {Text} from 'react-native';

const getFullName = (
  firstName: string,
  secondName: string,
  thirdName: string,
) => {
  return firstName + ' ' + secondName + ' ' + thirdName;
};

const Cat = () => {
  return <Text>Hello, I am {getFullName('Rum', 'Tum', 'Tugger')}!</Text>;
};

export default Cat;
```

</TabItem>
</Tabs>

中括弧は、JSXのJS機能へのポータルを作成するものと考えることができます！

> JSXはReactライブラリに含まれているので、ファイルの先頭に「Reactを 'react'からインポートする」がないと機能しません！

## Custom Components

[React NativeのCore Components]（イントロ・リアクト・ネイティブ・コンポーネント）についてはすでに説明しました。Reactでは、これらのコンポーネントを互いにネストして新しいコンポーネントを作成できます。これらのネスト可能で再利用可能なコンポーネントは、Reactパラダイムの中心です。

たとえば、下の [`View`] (ビュー) の中に [`Text`] (テキスト) と [`TextInput`] (TextInput) をネストすると、React Nativeはそれらを一緒にレンダリングします。

```SnackPlayer name=Custom%20Components
import React from 'react';
import {Text, TextInput, View} from 'react-native';

const Cat = () => {
  return (
    <View>
      <Text>Hello, I am...</Text>
      <TextInput
        style={{
          height: 40,
          borderColor: 'gray',
          borderWidth: 1,
        }}
        defaultValue="Name me!"
      />
    </View>
  );
};

export default Cat;
```

#### Developer notes

<Tabs groupId="guide" queryString defaultValue="web" values={constants.getDevNotesTabs(["android", "web"])}>

<TabItem value="web">

> ウェブ開発に精通しているなら、`<View>`と `<Text>` はHTMLを連想させるかもしれません！それらはアプリケーション開発の「`<div>`」と「`<p>`」タグと考えることができます。
</TabItem>
<TabItem value="android">

> Androidでは、通常、ビューを「リニアレイアウト」、「フレームレイアウト」、「相対レイアウト」などの中に配置して、ビューの子を画面上でどのように配置するかを定義します。React Nativeでは、「View」は子供用のレイアウトにフレックスボックスを使用しています。詳細については、[フレックスボックスを使用したレイアウトガイド]（フレックスボックス）をご覧ください。
</TabItem>
</Tabs>
`<Cat>` を使うと、コードを繰り返さなくても、このコンポーネントを複数回、複数の場所でレンダリングできます。

```SnackPlayer name=Multiple%20Components
import React from 'react';
import {Text, View} from 'react-native';

const Cat = () => {
  return (
    <View>
      <Text>I am also a cat!</Text>
    </View>
  );
};

const Cafe = () => {
  return (
    <View>
      <Text>Welcome!</Text>
      <Cat />
      <Cat />
      <Cat />
    </View>
  );
};

export default Cafe;
```

他のコンポーネントをレンダリングするコンポーネントはすべて**親コンポーネントです。** ここでは、`Cafe`は親コンポーネントで、各 `Cat`は**子コンポーネントです。**

カフェには好きなだけ猫をたくさん入れることができます。各 `<Cat>` は固有の要素をレンダリングします。小道具でカスタマイズできます。

## Props

**Props**は「プロパティ」の略です。小道具を使うと、Reactコンポーネントをカスタマイズできます。たとえば、ここでは、レンダリングする `<Cat>`にそれぞれ異なる `名前`を渡します。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Multiple%20Props&ext=js
import React from 'react';
import {Text, View} from 'react-native';

const Cat = props => {
  return (
    <View>
      <Text>Hello, I am {props.name}!</Text>
    </View>
  );
};

const Cafe = () => {
  return (
    <View>
      <Cat name="Maru" />
      <Cat name="Jellylorum" />
      <Cat name="Spot" />
    </View>
  );
};

export default Cafe;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Multiple%20Props&ext=tsx
import React from 'react';
import {Text, View} from 'react-native';

type CatProps = {
  name: string;
};

const Cat = (props: CatProps) => {
  return (
    <View>
      <Text>Hello, I am {props.name}!</Text>
    </View>
  );
};

const Cafe = () => {
  return (
    <View>
      <Cat name="Maru" />
      <Cat name="Jellylorum" />
      <Cat name="Spot" />
    </View>
  );
};

export default Cafe;
```

</TabItem>
</Tabs>

React NativeのCore Components ほとんどは、小道具を使ってカスタマイズすることもできます。たとえば、[`Image`] (image) を使うときは、[`source`] (image #source) という名前のプロップを渡して、表示されるImage を定義します。

```SnackPlayer name=Props
import React from 'react';
import {Text, View, Image} from 'react-native';

const CatApp = () => {
  return (
    <View>
      <Image
        source={{
          uri: 'https://reactnative.dev/docs/assets/p_cat1.png',
        }}
        style={{width: 200, height: 200}}
      />
      <Text>Hello, I am your cat!</Text>
    </View>
  );
};

export default CatApp;
```

`Image`には、デザインのJSオブジェクトとレイアウト関連のプロパティと値のペアを受け入れる [`style`] (image #style) を含む [さまざまな小道具] (image #props) があります。

> `style`の幅と高さを二重中括弧 `{{}}` で囲んでいることに注目してください。JSXでは、JavaScriptの値は `{}` で参照されます。これは、配列や数値など、文字列以外のものを小道具として渡す場合に便利です:``<Cat food= {["fish」,「kibble"]} age= {2} />。ただし、JSオブジェクトは**_also_**で中括弧で示されます：`{幅：200、高さ：200}`。したがって、JSXでJSオブジェクトを渡すには、オブジェクトを**別のペア**の中括弧で囲む必要があります：`{{幅：200、高さ：200}}`

小道具とCore Components [`Text`] (テキスト)、[`Image`] (Image)、[`View`] (ビュー) を使ってたくさんのものを構築できます!しかし、インタラクティブなものを構築するには、州が必要です。

## State

propsはコンポーネントのレンダリング方法を設定するために使用する引数と考えることができますが、**state**はコンポーネントの個人データストレージのようなものです。Stateは、時間の経過とともに変化するデータやユーザーの操作から生じるデータを処理するのに役立ちます。状態はコンポーネントにメモリを与えます！

> 原則として、コンポーネントをレンダリングするときに小道具を使って構成します。stateを使用して、時間の経過とともに変化すると予想されるコンポーネントデータを追跡します。

次の例は、空腹の2匹の猫が餌を待っている猫カフェを舞台にしています。（名前とは異なり）時間の経過とともに変化すると予想される彼らの空腹感は、状態として保存されます。猫に餌をやるには、ボタンを押します。そうすると猫の状態が更新されます。

[Reactの `useState`フック] (https://react.dev/learn/state-a-components-memory) を呼び出すことで、コンポーネントに状態を追加することができます。フックは、Reactの機能に「フックする」ことができる一種の機能です。たとえば、`useState`は関数コンポーネントに状態を追加できるフックです。[他の種類のフック] について詳しくは、Reactのドキュメントをご覧ください。(https://react.dev/reference/react)

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=State&ext=js
import React, {useState} from 'react';
import {Button, Text, View} from 'react-native';

const Cat = props => {
  const [isHungry, setIsHungry] = useState(true);

  return (
    <View>
      <Text>
        I am {props.name}, and I am {isHungry ? 'hungry' : 'full'}!
      </Text>
      <Button
        onPress={() => {
          setIsHungry(false);
        }}
        disabled={!isHungry}
        title={isHungry ? 'Pour me some milk, please!' : 'Thank you!'}
      />
    </View>
  );
};

const Cafe = () => {
  return (
    <>
      <Cat name="Munkustrap" />
      <Cat name="Spot" />
    </>
  );
};

export default Cafe;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=State&ext=tsx
import React, {useState} from 'react';
import {Button, Text, View} from 'react-native';

type CatProps = {
  name: string;
};

const Cat = (props: CatProps) => {
  const [isHungry, setIsHungry] = useState(true);

  return (
    <View>
      <Text>
        I am {props.name}, and I am {isHungry ? 'hungry' : 'full'}!
      </Text>
      <Button
        onPress={() => {
          setIsHungry(false);
        }}
        disabled={!isHungry}
        title={isHungry ? 'Pour me some milk, please!' : 'Thank you!'}
      />
    </View>
  );
};

const Cafe = () => {
  return (
    <>
      <Cat name="Munkustrap" />
      <Cat name="Spot" />
    </>
  );
};

export default Cafe;
```

</TabItem>
</Tabs>
まず、次のようにReactから `useState`をインポートしたいと思うでしょう。

```tsx
import React, {useState} from 'react';
```

次に、関数内で `useState`を呼び出してコンポーネントの状態を宣言します。この例では、「useState」は「IsHungry」状態変数を作成します。

```tsx
const Cat = (props: CatProps) => {
  const [isHungry, setIsHungry] = useState(true);
  // ...
};
```

> `UseState`を使うと、文字列、数値、ブール値、配列、オブジェクトなど、あらゆる種類のデータを追跡できます。たとえば、猫が「const [timeSpetted, setTimeSpetted] = useState (0) `で撫でられた回数を追跡できます!

`useState`を呼び出すと、次の2つのことが行われます。

-初期値で「状態変数」を作成します。この場合、状態変数は `Ishungry`で、初期値は `true`です
-その状態変数の値を設定する関数を作成します— `setIsHungry`

どんな名前を使ってもかまいません。しかし、パターンを `[<getter>,<setter>] = useState (<initialValue>)` と考えると便利です。

次に、[`Button`] (button) コアコンポーネントを追加して、それに `onPress` プロップを指定します。

```tsx
<Button
  onPress={() => {
    setIsHungry(false);
  }}
  //..
/>
```

さて、誰かがボタンを押すと、「onPress」が起動し、「SetIsHungry (false)」が呼び出されます。これにより、状態変数 `Ishungry`が`false`に設定されます。`Ishungry`がfalseの場合、「ボタン」の「無効」プロップは「true」に設定され、その「タイトル」も変わります。

```tsx
<Button
  //..
  disabled={!isHungry}
  title={isHungry ? 'Pour me some milk, please!' : 'Thank you!'}
/>
```

>`Ishungry` は [const] (https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/const) ですが、一見再割り当て可能であることにお気づきかもしれません!何が起こっているかというと、`SetIsHungry`のような状態設定関数が呼び出されると、そのコンポーネントが再レンダリングされます。この場合、「Cat」関数が再び実行されます。今回は「useState」によって、次の値は「IsHungry」になります。

最後に、猫を「カフェ」コンポーネントの中に入れてください。

```tsx
const Cafe = () => {
  return (
    <>
      <Cat name="Munkustrap" />
      <Cat name="Spot" />
    </>
  );
};
```

>上記の `<>` と `</>` を参照してください？JSXのこれらのビットは [フラグメント] (https://react.dev/reference/react/Fragment) です。隣接するJSX要素は、囲みタグで囲む必要があります。フラグメントを使用すると、`View`のような余分な不要なラッピング要素をネストせずにそれを行うことができます。

---

ReactとReact NativeのCore Components の両方について説明したので、これらのコアコンポーネントのいくつかについて、[`<TextInput>`handling]（ハンドリング-テキスト入力）を見て詳しく見ていきましょう。
