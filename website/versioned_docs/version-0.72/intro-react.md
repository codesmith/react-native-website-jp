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

手順は次のとおりです。`Cat`コンポーネントを定義するには、まずJavaScriptの [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) を使ってReactをインポートし、React Nativeの [`Text`](/docs/next/text) コアコンポーネントをインポートしてください。

```tsx
import React from 'react';
import {Text} from 'react-native';
```

コンポーネントは関数として始まります:

```tsx
const Cat = () => {};
```

コンポーネントは設計図と考えることができます。関数コンポーネントが返すものはすべて、**React要素**としてレンダリングされます。React要素で、画面に表示したいものを記述することができます。

ここで、`Cat`コンポーネントは `<Text>`要素をレンダリングします：

```tsx
const Cat = () => {
  return <Text>Hello, I am your cat!</Text>;
};
```

JavaScriptの [`export default`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export) を使用して関数コンポーネントをエクスポートして、次のようにアプリ全体で使用できます。

```tsx
const Cat = () => {
  return <Text>Hello, I am your cat!</Text>;
};

export default Cat;
```

> これは、コンポーネントをエクスポートする多くの方法の1つです。この種のエクスポートは、Snack Player でうまく機能します。ただし、アプリのファイル構造によっては、別の規則を使用する必要があるかもしれません。この [JavaScriptのインポート/エクスポートに関する便利なチートシート](https://medium.com/dailyjs/javascript-module-cheatsheet-7bd474f1d829) が役に立ちます。

それでは、その `return`ステートメントを詳しく見てみましょう。`<Text>Hello, I am your cat!</Text>`は、簡便に要素を書くことができる、JavaScript記法の一種である JSX を使用しています。

## JSX

ReactとReact Nativeは、**JSX**という構文を使用して、JavaScriptの内部に次のように要素を書くことができます。`<Text>Hello, I am your cat!</Text>`。Reactのドキュメントには [JSXの総合ガイド](https://ja.react.dev/learn/writing-markup-with-jsx) がありますので、参照してさらに詳しく学ぶことができます。JSXはJavaScriptなので、その中で変数を使うことができます。ここでは、猫の名前`name`を宣言し、それを `<Text>` の中に中括弧で埋め込んでいます。

```SnackPlayer name=Curly%20Braces
import React from 'react';
import {Text} from 'react-native';

const Cat = () => {
  const name = 'Maru';
  return <Text>Hello, I am {name}!</Text>;
};

export default Cat;
```

`{getFullName("Rum", "Tum", "Tugger")}` のような関数呼び出しを含め、どのJavaScript式も中括弧で囲んで使用できます。

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

> JSXはReactライブラリに含まれているので、ファイルの先頭に`import React from 'react'`がないと機能しません！

## Custom Components

[React NativeのCore Components](intro-react-native-components)についてはすでに説明しました。Reactでは、これらのコンポーネントを互いにネストして新しいコンポーネントを作成できます。これらのネスト可能で再利用可能なコンポーネントは、Reactパラダイムの中心概念です。

たとえば、下の [`View`](text)の中に [`Text`](テキスト) と [`TextInput`](textinput) をネストすると、React Nativeはそれらを一緒にレンダリングします。

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

> ウェブ開発に精通しているなら、`<View>`と `<Text>` はHTMLを連想させるかもしれません！それらはアプリケーション開発の`<div>`と`<p>`タグと考えることができます。
</TabItem>
<TabItem value="android">

> Androidでは、通常、ビューを`LinearLayout`、`FrameLayout`、`RelativeLayout`などの中に配置して、ビューの子を画面上でどのように配置するかを定義します。React Nativeでは、`View` は子のレイアウトをするためにFlexboxを使用しています。詳細については、[フレックスボックスを使用したレイアウトガイド](flexbox) をご覧ください。
</TabItem>
</Tabs>
`<Cat>` を使うことで、コードを繰り返さなくても、このコンポーネントを複数回、複数の場所でレンダリングすることができます。

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

他のコンポーネントをレンダリングするコンポーネントはすべて**親コンポーネントです。** ここでは、`Cafe`は親コンポーネントで、各 `Cat`は**子コンポーネント**です。

カフェには好きなだけ猫をたくさん入れることができます。各 `<Cat>` はそれぞれ固有の要素をレンダリングします。props でカスタマイズできます。

## Props

**Props**は `properties`の略です。propsを使うと、Reactコンポーネントをカスタマイズできます。たとえば、ここでは、レンダリングする `<Cat>`にそれぞれ異なる `名前`を渡します。

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

React NativeのほとんどのCore Components も、propsを使ってカスタマイズすることができます。たとえば、[`Image`](image) を使うときは、[`source`](image#source) という名前の prop を渡して、表示されるイメージ を定義します。

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

`Image`には、デザインのJSオブジェクトとレイアウト関連のプロパティと値のペアを受け入れる [`style`](image#style) を含む [さまざまなprops](image#props) があります。

> `style`の幅と高さを二重中括弧 `{{ }}` で囲んでいることに注目してください。JSXでは、JavaScriptの値は `{}` で参照されます。これは、配列や数値など、文字列以外のものをpropsとして渡す場合に便利です:`<Cat food={["fish`,`kibble"]} age={2} />`。ただし、JSオブジェクト**も**で中括弧で示されます：`{width: 200, height: 200}`。したがって、JSXでJSオブジェクトを渡すには、オブジェクトを**別のペア**の中括弧で囲む必要があります：`{{width: 200, height: 200}}`

propsとCore Componentsの [`Text`](text)、[`Image`](image)、[`View`](view) を使ってたくさんのものを構築できます！しかし、インタラクティブなものを構築するには、state が必要です。

## State

propsはコンポーネントのレンダリング方法を設定するために使用する引数と考えることができますが、**state**はコンポーネントの個人データストレージのようなものです。stateは、時間の経過とともに変化するデータやユーザーの操作から生じるデータを処理するのに役立ちます。stateはコンポーネントにメモリを与えます！

> 原則として、コンポーネントをレンダリングするときはpropsを使って構成します。時間の経過とともに変化すると予想されるコンポーネントデータを追跡するときはstateを使用します。

次の例は、空腹の2匹の猫が餌を待っている猫カフェを舞台にしています。（名前とは違って）時間の経過とともに変化すると予想される彼らの空腹感は、state として保存されます。猫に餌をやるには、ボタンを押します。そうすると猫のstateが更新されます。

[Reactの `useState`フック](https://react.dev/learn/state-a-components-memory) を呼び出すことで、コンポーネントにstateを追加することができます。フックは、Reactの機能に`フックする`ことができる一種の機能です。たとえば、`useState`は関数コンポーネントにstateを追加できるフックです。他の種類のフックについて詳しくは、[Reactのドキュメント](https://ja.react.dev/reference/react)をご覧ください。

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

次に、関数内で `useState`を呼び出してコンポーネントのstateを宣言します。この例では、`useState`は`isHungry`state変数を作成します。

```tsx
const Cat = (props: CatProps) => {
  const [isHungry, setIsHungry] = useState(true);
  // ...
};
```

> `useState`を使うと、文字列、数値、ブール値、配列、オブジェクトなど、あらゆる種類のデータを追跡できます。たとえば、猫が撫でられた回数を`const [timesPetted, setTimesPetted] = useState(0)`で追跡できます!

`useState`を呼び出すと、次の2つのことが行われます。

- 初期値で`state変数`を作成します。この場合、state変数は `isHungry`で、初期値は `true`です
- そのstate変数の値を設定する関数を作成します— `setIsHungry`

どんな名前を使ってもかまいません。しかし、パターンを `[<getter>, <setter>] = useState(<initialValue>)` と考えると便利です。

次に、[`Button`](button) コアコンポーネントを追加して、それに `onPress` propを指定します。

```tsx
<Button
  onPress={() => {
    setIsHungry(false);
  }}
  //..
/>
```

さて、誰かがボタンを押すと、`onPress`が起動し、`setIsHungry(false)`が呼び出されます。これにより、state変数 `isHungry`が`false`に設定されます。`isHungry`がfalseの場合、`Button`の`無効`propは`true`に設定され、その`title`も変わります。

```tsx
<Button
  //..
  disabled={!isHungry}
  title={isHungry ? 'Pour me some milk, please!' : 'Thank you!'}
/>
```

>`isHungry` は [const](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/const) ですが、一見再割り当て可能であることにお気づきかもしれません！何が起こっているかというと、`setIsHungry`のようなstate設定関数が呼び出されると、そのコンポーネントが再レンダリングされます。この場合、`Cat`関数が再び実行されます。今回は`useState`によって、次の値は`isHungry`になります。

最後に、猫を`Cafe`コンポーネントの中に入れてください。

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

>上記の `<>` と `</>` を着目してください。JSXのこれらのビットは [フラグメント](https://ja.react.dev/reference/react/Fragment) です。隣接するJSX要素は、囲みタグで囲む必要があります。フラグメントを使用すると、`View`のような余分な不要なラッピング要素をネストせずにそれを行うことができます。

---

ReactとReact NativeのCore Components の両方について説明したので、これらのコアコンポーネントのいくつかについて、[handling `<TextInput>`](handling-text-input)を見て詳しく見ていきましょう。
