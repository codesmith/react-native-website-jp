---
id: intro-react-native-components
title: Core Components and Native Components
description: 'React Native lets you compose app interfaces using Native Components. Conveniently, it comes with a set of these components for you to get started with right now—the Core Components!'
---

import ThemedImage from '@theme/ThemedImage';

React Nativeは、[React](https://reactjs.org/) とアプリプラットフォームのネイティブ機能を使用してAndroidおよびiOSアプリケーションを構築するためのオープンソースフレームワークです。React Nativeでは、JavaScriptを使用してプラットフォームのAPIにアクセスしたり、Reactコンポーネント（再利用可能なネスト可能なコードのバンドル）を使用してUIの外観と動作を記述したりできます。Reactについては次のセクションで詳しく学ぶことができます。しかし、その前に、React Nativeでコンポーネントがどのように機能するかについて説明しましょう。

## Views and mobile development

AndroidとiOSの開発では、**ビュー**はUIの基本的な構成要素です。テキストや画像を表示したり、ユーザー入力に応答したりするために使用できる画面上の小さな長方形の要素です。テキスト行やボタンなど、アプリの最小の視覚要素でさえ、ビューの一種です。ビューの種類によっては、他のビューを含むことができます。ずっと下の階層まで見通すことができます！

<figure>
  <img src="/docs/assets/diagram_ios-android-views.svg" width="1000" alt="Diagram of Android and iOS app showing them both built on top of atomic elements called views." />
  <figcaption>Just a sampling of the many views used in Android and iOS apps.</figcaption>
</figure>

## Native Components

Androidの開発では、KotlinまたはJavaでビューを作成します。iOSの開発では、SwiftまたはObjective-Cを使用します。React Nativeでは、Reactコンポーネントを使用してJavaScriptでこれらのビューを呼び出すことができます。実行時に、React Nativeはこれらのコンポーネントに対応するAndroidビューとiOSビューを作成します。React NativeコンポーネントはAndroidやiOSと同じビューに支えられているため、React Nativeアプリは他のアプリと同じような外観、操作性、パフォーマンスを発揮します。これらのプラットフォームベースのコンポーネントを**Native Components** と呼んでいます。

React Nativeには、すぐに使える必須のNative Componentsが付属しており、今日からアプリの構築を始めることができます。これらはReact Nativeの**Core Components**です。

React Nativeでは、アプリ固有のニーズに合わせて、[Android](native-components-android.md) 用と [iOS](native-components-ios.md)用の独自のネイティブコンポーネントを構築することもできます。また、それとは別に**community-contributed components**のエコシステムも盛んです。 [Native Directory](https://reactnative.directory) をチェックして、コミュニティが作成してきたものを見てみてください。

## Core Components

React Nativeには、コントロールからアクティビティインジケータまで、あらゆるものに対応する多くのCore Components があります。それらはすべて [APIセクションに記載](components-and-apis)に記載があります。ほとんどの場合、次のCore Components を使用します。

| React Native UI Component | Android View   | iOS View         | Web Analog              | Description                                                                                           |
| ------------------------- | -------------- | ---------------- | ----------------------- | ----------------------------------------------------------------------------------------------------- |
| `<View>`                  | `<ViewGroup>`  | `<UIView>`       | A non-scrolling `<div>` | A container that supports layout with flexbox, style, some touch handling, and accessibility controls |
| `<Text>`                  | `<TextView>`   | `<UITextView>`   | `<p>`                   | Displays, styles, and nests strings of text and even handles touch events                             |
| `<Image>`                 | `<ImageView>`  | `<UIImageView>`  | `<img>`                 | Displays different types of images                                                                    |
| `<ScrollView>`            | `<ScrollView>` | `<UIScrollView>` | `<div>`                 | A generic scrolling container that can contain multiple components and views                          |
| `<TextInput>`             | `<EditText>`   | `<UITextField>`  | `<input type="text">`   | Allows the user to enter text                                                                         |

次のセクションでは、これらのCore Components を組み合わせて、Reactがどのように機能するかを学びます。今、ここで彼らと遊んでみてください！

```SnackPlayer name=Hello%20World
import React from 'react';
import {View, Text, Image, ScrollView, TextInput} from 'react-native';

const App = () => {
  return (
    <ScrollView>
      <Text>Some text</Text>
      <View>
        <Text>Some more text</Text>
        <Image
          source={{
            uri: 'https://reactnative.dev/docs/assets/p_cat2.png',
          }}
          style={{width: 200, height: 200}}
        />
      </View>
      <TextInput
        style={{
          height: 40,
          borderColor: 'gray',
          borderWidth: 1,
        }}
        defaultValue="You can type in me"
      />
    </ScrollView>
  );
};

export default App;
```

---

React NativeはReactコンポーネントと同じAPI構造を使用しているので、始めるにはReactコンポーネントのAPIを理解する必要があります。[次のセクション](intro-react)では、このトピックについて簡単に紹介したり、復習したりできます。ただし、Reactに既に習熟している場合は、遠慮なく [先に進んでください](handling-text-input)。

<ThemedImage
alt="A diagram showing React Native's Core Components are a subset of React Components that ship with React Native."
sources={{
  light: '/docs/assets/diagram_react-native-components.svg',
  dark: '/docs/assets/diagram_react-native-components_dark.svg',
}}
/>
