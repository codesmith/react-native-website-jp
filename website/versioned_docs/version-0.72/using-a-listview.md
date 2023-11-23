---
id: using-a-listview
title: Using List Views
---

React Native は、データのリストを表示するためのコンポーネントスイートを提供します。一般的には、[FlatList](flatlist.md) または [sectionList](sectionlist.md) のどちらかを使用したいと思うでしょう。

`FlatList`コンポーネントは、変化しているが構造が似ているデータのスクロールリストを表示します。`FlatList`は、時間の経過とともに項目の数が変化する可能性のある長いデータリストを表示するのに適しています。より一般的な[`ScrollView`](using-a-scrollview.md)とは異なり、`FlatList`は現在画面に表示されている要素のみをレンダリングし、すべての要素を一度にレンダリングしません。

`FlatList`コンポーネントには、`data` と `renderItem`という2つの props が必要です。`data`はリストの情報源です。`renderItem`はソースから1つのアイテムを取り出し、レンダリングするためのフォーマットされたコンポーネントを返します。

下の例では、ハードコードされたデータの基本的な`FlatList`を作成します。`data` propの各項目は、`Text`コンポーネントとしてレンダリングされます。次に、`FlatListBasics`コンポーネントは`FlatList`コンポーネントとすべての`Text`コンポーネントをレンダリングします。

```SnackPlayer name=FlatList%20Basics
import React from 'react';
import {FlatList, StyleSheet, Text, View} from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: 22,
  },
  item: {
    padding: 10,
    fontSize: 18,
    height: 44,
  },
});

const FlatListBasics = () => {
  return (
    <View style={styles.container}>
      <FlatList
        data={[
          {key: 'Devin'},
          {key: 'Dan'},
          {key: 'Dominic'},
          {key: 'Jackson'},
          {key: 'James'},
          {key: 'Joel'},
          {key: 'John'},
          {key: 'Jillian'},
          {key: 'Jimmy'},
          {key: 'Julie'},
        ]}
        renderItem={({item}) => <Text style={styles.item}>{item.key}</Text>}
      />
    </View>
  );
};

export default FlatListBasics;
```

iOSの `UITableView` のように、論理セクションに分割されたデータのセットをセクションヘッダーなどでレンダリングしたい場合は、[sectionList](sectionlist.md) が最適です。

```SnackPlayer name=SectionList%20Basics
import React from 'react';
import {SectionList, StyleSheet, Text, View} from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: 22,
  },
  sectionHeader: {
    paddingTop: 2,
    paddingLeft: 10,
    paddingRight: 10,
    paddingBottom: 2,
    fontSize: 14,
    fontWeight: 'bold',
    backgroundColor: 'rgba(247,247,247,1.0)',
  },
  item: {
    padding: 10,
    fontSize: 18,
    height: 44,
  },
});

const SectionListBasics = () => {
  return (
    <View style={styles.container}>
      <SectionList
        sections={[
          {title: 'D', data: ['Devin', 'Dan', 'Dominic']},
          {
            title: 'J',
            data: [
              'Jackson',
              'James',
              'Jillian',
              'Jimmy',
              'Joel',
              'John',
              'Julie',
            ],
          },
        ]}
        renderItem={({item}) => <Text style={styles.item}>{item}</Text>}
        renderSectionHeader={({section}) => (
          <Text style={styles.sectionHeader}>{section.title}</Text>
        )}
        keyExtractor={item => `basicListEntry-${item}`}
      />
    </View>
  );
};

export default SectionListBasics;
```

リストビューの最も一般的な用途の1つは、サーバーから取得したデータを表示することです。そのためには、[React Native のネットワークについて学ぶ](network.md) 必要があります。
