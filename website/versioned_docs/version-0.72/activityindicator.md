---
id: activityindicator
title: ActivityIndicator
---

ロード中であることを示す円形のインジケータを表示します。

## Example

```SnackPlayer name=ActivityIndicator%20Example
import React from 'react';
import {ActivityIndicator, StyleSheet, View} from 'react-native';

const App = () => (
  <View style={[styles.container, styles.horizontal]}>
    <ActivityIndicator />
    <ActivityIndicator size="large" />
    <ActivityIndicator size="small" color="#0000ff" />
    <ActivityIndicator size="large" color="#00ff00" />
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
  },
  horizontal: {
    flexDirection: 'row',
    justifyContent: 'space-around',
    padding: 10,
  },
});

export default App;
```

# Reference

## Props

### [View Props](view#props)

[View Props](view#props) を継承します。

---

### `animating`

インジケーターを表示するか（`true`）、非表示にする（`false`）か。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `color`

スピナーのフォアグラウンドカラー。

| Type            | Default                                                                                                                                                                                     |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [color](colors) | `null` (system accent default color)<div class="label android">Android</div><hr/><ins style={{background: '#999'}} className="color-box" />`'#999999'` <div className="label ios">iOS</div> |

---

### `hidesWhenStopped` <div class="label ios">iOS</div>

アニメーション化していないときにインジケーターを非表示にするかどうか。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `size`

インジケータのサイズ。

| Type                                                                           | Default   |
| ------------------------------------------------------------------------------ | --------- |
| enum(`'small'`, `'large'`)<hr/>number <div class="label android">Android</div> | `'small'` |
