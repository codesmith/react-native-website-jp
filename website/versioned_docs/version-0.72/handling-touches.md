---
id: handling-touches
title: Handling Touches
---

ユーザーは主にタッチでモバイルアプリを操作します。ボタンのタップ、リストのスクロール、地図のズームなど、さまざまなジェスチャーを組み合わせて使用できます。React Native には、ありとあらゆる種類の一般的なジェスチャを処理するコンポーネントと、より高度なジェスチャ認識を可能にする包括的な [gesture responder system](gesture-responder-system.md) が用意されています。しかし、最も興味があるコンポーネントは基本的な Button です。

## Displaying a basic button

[Button](button.md) には、すべてのプラットフォームでうまくレンダリングされる基本的なボタンコンポーネントが用意されています。ボタンを表示する最小限の例は次のようになります。

```tsx
<Button
  onPress={() => {
    console.log('You tapped the button!');
  }}
  title="Press Me"
/>
```

これにより、iOS では青いラベルがレンダリングされ、Android では明るいテキストが付いた青い丸い長方形がレンダリングされます。ボタンを押すと「onPress」関数が呼び出され、この場合はアラートポップアップが表示されます。必要に応じて、「color」prop を指定してボタンの色を変更できます。

![](/docs/assets/Button.png)

さあ、以下の例を使って `Button` コンポーネントを試してみてください。アプリをプレビューするプラットフォームを選択するには、右下のトグルをクリックし、「タップして再生」をクリックしてアプリをプレビューします。

```SnackPlayer name=Button%20Basics
import React, {Component} from 'react';
import {Alert, Button, StyleSheet, View} from 'react-native';

export default class ButtonBasics extends Component {
  _onPressButton() {
    Alert.alert('You tapped the button!');
  }

  render() {
    return (
      <View style={styles.container}>
        <View style={styles.buttonContainer}>
          <Button onPress={this._onPressButton} title="Press Me" />
        </View>
        <View style={styles.buttonContainer}>
          <Button
            onPress={this._onPressButton}
            title="Press Me"
            color="#841584"
          />
        </View>
        <View style={styles.alternativeLayoutButtonContainer}>
          <Button onPress={this._onPressButton} title="This looks great!" />
          <Button onPress={this._onPressButton} title="OK!" color="#841584" />
        </View>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
  },
  buttonContainer: {
    margin: 20,
  },
  alternativeLayoutButtonContainer: {
    margin: 20,
    flexDirection: 'row',
    justifyContent: 'space-between',
  },
});
```

## Touchables

基本的なボタンがアプリに合わない場合は、React Native が提供する「Touchable」コンポーネントのいずれかを使用して独自のボタンを作成できます。「Touchable」コンポーネントはタップジェスチャーをキャプチャする機能を提供し、ジェスチャーが認識されたときにフィードバックを表示できます。ただし、これらのコンポーネントにはデフォルトのスタイルがないため、アプリ内で見栄えを良くするには少し作業が必要です。

どの「Touchable」コンポーネントを使用するかは、提供したいフィードバックの種類によって異なります。

- 一般に、[**TouchableHighlight**](touchablehighlight.md) はウェブ上のボタンやリンクを使用する場所ならどこでも使用できます。ユーザーがボタンを押すと、ビューの背景が暗くなります。

- ユーザーのタッチに反応するインクの表面反応波紋を表示するには、Android で [**TouchableNativeFeedback**](touchablenativefeedback.md) を使用することを検討してください。

- [**TouchableOpacity**](touchableopacity.md) を使用すると、ボタンの不透明度を下げて、ユーザーがボタンを押している間も背景が透けて見えるようにすることでフィードバックを提供できます。

- タップジェスチャを処理する必要があるが、フィードバックは表示したくない場合は、[**TouchableWithoutFeedback**](touchablewithoutfeedback.md) を使用してください。

場合によっては、ユーザーがビューを一定時間押したままにしたことを検出したい場合があります。このような長押しは、任意の「Touchable」コンポーネントの `onLongPress` プロップに関数を渡すことで処理できます。

これらすべてを実際に見てみましょう。:

```SnackPlayer name=Touchables
import React, {Component} from 'react';
import {
  Alert,
  Platform,
  StyleSheet,
  Text,
  TouchableHighlight,
  TouchableOpacity,
  TouchableNativeFeedback,
  TouchableWithoutFeedback,
  View,
} from 'react-native';

export default class Touchables extends Component {
  _onPressButton() {
    Alert.alert('You tapped the button!');
  }

  _onLongPressButton() {
    Alert.alert('You long-pressed the button!');
  }

  render() {
    return (
      <View style={styles.container}>
        <TouchableHighlight onPress={this._onPressButton} underlayColor="white">
          <View style={styles.button}>
            <Text style={styles.buttonText}>TouchableHighlight</Text>
          </View>
        </TouchableHighlight>
        <TouchableOpacity onPress={this._onPressButton}>
          <View style={styles.button}>
            <Text style={styles.buttonText}>TouchableOpacity</Text>
          </View>
        </TouchableOpacity>
        <TouchableNativeFeedback
          onPress={this._onPressButton}
          background={
            Platform.OS === 'android'
              ? TouchableNativeFeedback.SelectableBackground()
              : undefined
          }>
          <View style={styles.button}>
            <Text style={styles.buttonText}>
              TouchableNativeFeedback{' '}
              {Platform.OS !== 'android' ? '(Android only)' : ''}
            </Text>
          </View>
        </TouchableNativeFeedback>
        <TouchableWithoutFeedback onPress={this._onPressButton}>
          <View style={styles.button}>
            <Text style={styles.buttonText}>TouchableWithoutFeedback</Text>
          </View>
        </TouchableWithoutFeedback>
        <TouchableHighlight
          onPress={this._onPressButton}
          onLongPress={this._onLongPressButton}
          underlayColor="white">
          <View style={styles.button}>
            <Text style={styles.buttonText}>Touchable with Long Press</Text>
          </View>
        </TouchableHighlight>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    paddingTop: 60,
    alignItems: 'center',
  },
  button: {
    marginBottom: 30,
    width: 260,
    alignItems: 'center',
    backgroundColor: '#2196F3',
  },
  buttonText: {
    textAlign: 'center',
    padding: 20,
    color: 'white',
  },
});
```

## Scrolling and swiping

タッチスクリーンを備えたデバイスで一般的に使用されるジェスチャーには、スワイプやパンが含まれます。これにより、ユーザーはアイテムのリストをスクロールしたり、コンテンツのページをスワイプしたりできます。これらについては、[ScrollView](scrollview.md) コアコンポーネント をチェックしてください。

## Known issues

- [react-native#29308](https://github.com/facebook/react-native/issues/29308#issuecomment-792864162): タッチエリアが親ビューの境界を超えることはなく、Android では負のマージンはサポートされていません。
