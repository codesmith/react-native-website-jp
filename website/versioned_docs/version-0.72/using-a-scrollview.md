---
id: using-a-scrollview
title: Using a ScrollView
---

[ScrollView](scrollview.md)は、複数のコンポーネントとビューを含むことができる一般的なスクロールコンテナです。スクロール可能な項目は異種でもよく、垂直方向と水平方向の両方にスクロールできます（`horizontal`プロパティを設定することで）。

この例では、画像とテキストの両方が混在した縦型の`ScrollView`を作成します。

```SnackPlayer name=Using%20ScrollView
import React from 'react';
import {Image, ScrollView, Text} from 'react-native';

const logo = {
  uri: 'https://reactnative.dev/img/tiny_logo.png',
  width: 64,
  height: 64,
};

const App = () => (
  <ScrollView>
    <Text style={{fontSize: 96}}>Scroll me plz</Text>
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Text style={{fontSize: 96}}>If you like</Text>
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Text style={{fontSize: 96}}>Scrolling down</Text>
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Text style={{fontSize: 96}}>What's the best</Text>
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Text style={{fontSize: 96}}>Framework around?</Text>
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Text style={{fontSize: 80}}>React Native</Text>
  </ScrollView>
);

export default App;
```

ScrollViewsは、`PagingEnabled`プロパティを使用して、スワイプジェスチャを使用してビューをページングできるように設定できます。ビュー間を水平方向にスワイプすることは、[ViewPager](https://github.com/react-native-community/react-native-viewpager) コンポーネントを使用してAndroidで実装することもできます。

iOSでは、1つのアイテムを含むScrollViewを使用して、ユーザーがコンテンツをズームすることができます。`maximumZoomScale` and `minimumZoomScale`のプロップを設定すると、ユーザーはピンチジェスチャーと展開ジェスチャーを使ってズームインやズームアウトができます。

ScrollViewは、限られたサイズのものをいくつか表示するのに最適です。`ScrollView`のすべての要素とビューは、現在画面に表示されていなくてもレンダリングされます。画面に収まらないアイテムのリストが長い場合は、代わりに`FlatList`を使用してください。それでは、次に [リストビューについて学ぶ](using-a-listview.md)しましょう。
