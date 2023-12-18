---
id: height-and-width
title: Height and Width
---

コンポーネントの高さと幅によって、画面上のサイズが決まります。

## Fixed Dimensions

コンポーネントの寸法を設定する一般的な方法は、スタイルに固定の「幅」と「高さ」を追加することです。React Native のすべての寸法は単位がなく、密度に依存しないピクセルを表します。

```SnackPlayer name=Height%20and%20Width
import React from 'react';
import {View} from 'react-native';

const FixedDimensionsBasics = () => {
  return (
    <View>
      <View
        style={{
          width: 50,
          height: 50,
          backgroundColor: 'powderblue',
        }}
      />
      <View
        style={{
          width: 100,
          height: 100,
          backgroundColor: 'skyblue',
        }}
      />
      <View
        style={{
          width: 150,
          height: 150,
          backgroundColor: 'steelblue',
        }}
      />
    </View>
  );
};

export default FixedDimensionsBasics;
```

この方法で寸法を設定するのは、画面サイズに基づいて計算するのではなく、常に数ポイントにサイズを固定する必要があるコンポーネントでは一般的です。

:::caution
点から物理的な測定単位への普遍的なマッピングはありません。つまり、固定寸法のコンポーネントは、デバイスや画面サイズが異なると、物理サイズが同じではない可能性があります。しかし、この違いはほとんどのユースケースでは気づきません。
:::

## Flex Dimensions

コンポーネントのスタイルで `flex`を使用すると、使用可能なスペースに応じてコンポーネントが動的に拡張および縮小されます。通常は、`flex: 1`を使用します。これは、同じ親を持つ他のコンポーネント間で均等に共有され、使用可能なスペースをすべて埋めるようにコンポーネントに指示します。指定する「フレックス」が大きいほど、コンポーネントが兄弟コンポーネントと比較して占めるスペースの比率が高くなります。

:::info
コンポーネントは、親のサイズが「0」より大きい場合にのみ、空きスペースを埋めるように拡張できます。親の「幅」と「高さ」または「フレックス」が固定されていない場合、親のサイズは「0」になり、「フレックス」の子は表示されません。
:::

```SnackPlayer name=Flex%20Dimensions
import React from 'react';
import {View} from 'react-native';

const FlexDimensionsBasics = () => {
  return (
    // Try removing the `flex: 1` on the parent View.
    // The parent will not have dimensions, so the children can't expand.
    // What if you add `height: 300` instead of `flex: 1`?
    <View style={{flex: 1}}>
      <View style={{flex: 1, backgroundColor: 'powderblue'}} />
      <View style={{flex: 2, backgroundColor: 'skyblue'}} />
      <View style={{flex: 3, backgroundColor: 'steelblue'}} />
    </View>
  );
};

export default FlexDimensionsBasics;
```

コンポーネントのサイズを制御できたら、次のステップは [画面上に配置する方法を学ぶ](flexbox.md)です。

## Percentage Dimensions

画面の特定の部分を埋めたいが、「フレックス」レイアウトを使用したくない場合は、コンポーネントのスタイルで**パーセント値**を使用できます。フレックスサイズと同様に、パーセンテージディメンションには定義されたサイズの親が必要です。

```SnackPlayer name=Percentage%20Dimensions
import React from 'react';
import {View} from 'react-native';

const PercentageDimensionsBasics = () => {
  // Try removing the `height: '100%'` on the parent View.
  // The parent will not have dimensions, so the children can't expand.
  return (
    <View style={{height: '100%'}}>
      <View
        style={{
          height: '15%',
          backgroundColor: 'powderblue',
        }}
      />
      <View
        style={{
          width: '66%',
          height: '35%',
          backgroundColor: 'skyblue',
        }}
      />
      <View
        style={{
          width: '33%',
          height: '50%',
          backgroundColor: 'steelblue',
        }}
      />
    </View>
  );
};

export default PercentageDimensionsBasics;
```
