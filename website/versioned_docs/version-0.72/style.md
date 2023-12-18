---
id: style
title: Style
---

React Native では、JavaScriptを使用してアプリケーションのスタイルを設定します。すべてのコアコンポーネントは、`style`という名前のprop を受け入れます。スタイル名と [values](colors.md)は通常、CSSがウェブ上でどのように機能するかと一致します。ただし、名前が`background-color`ではなく`backgroundColor`のようにキャメルケースを使用して書かれている点が異なります。

`style`prop は、昔ながらのJavaScriptオブジェクトでもかまいません。これは、たとえばコードなど、私たちが通常使用するものです。スタイルの配列を渡すこともできます-配列の最後のスタイルが優先されるので、これを使ってスタイルを継承することができます。

コンポーネントが複雑になるにつれて、`StyleSheet.create`を使用して複数のスタイルを1か所で定義する方がわかりやすいことがよくあります。これが例です：

```SnackPlayer name=Style
import React from 'react';
import {StyleSheet, Text, View} from 'react-native';

const LotsOfStyles = () => {
  return (
    <View style={styles.container}>
      <Text style={styles.red}>just red</Text>
      <Text style={styles.bigBlue}>just bigBlue</Text>
      <Text style={[styles.bigBlue, styles.red]}>bigBlue, then red</Text>
      <Text style={[styles.red, styles.bigBlue]}>red, then bigBlue</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    marginTop: 50,
  },
  bigBlue: {
    color: 'blue',
    fontWeight: 'bold',
    fontSize: 30,
  },
  red: {
    color: 'red',
  },
});

export default LotsOfStyles;
```

一般的なパターンの1つは、コンポーネントに `style` prop を受け入れさせ、それがサブコンポーネントのスタイル設定に使用されるようにすることです。これを使って、CSSのようにスタイルを「カスケード」することができます。

テキストスタイルをカスタマイズする方法は他にもたくさんあります。完全なリストについては、[Text コンポーネントリファレンス](text.md)を参照してください。

これで、テキストを美しくすることができます。スタイルの専門家になるための次のステップは、[コンポーネントのサイズを制御する方法を学ぶ](height-and-width.md)です。

## Known issues

- [react-native#29308](https://github.com/facebook/react-native/issues/29308#issuecomment-792864162): 場合によっては、React Native がウェブ上でのCSSの動作と一致しないことがあります。たとえば、タッチエリアが親ビューの境界を超えて広がることはなく、Androidでは負のマージンがサポートされていません。
