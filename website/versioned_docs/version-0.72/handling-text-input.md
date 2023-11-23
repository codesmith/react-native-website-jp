---
id: handling-text-input
title: Handling Text Input
---

[`TextInput`](textinput#content) は、ユーザーがテキストを入力できる [Core Component](intro-react-native-components) です。テキストが変更されるたびに呼び出される関数を受け取る `onChangeText` prop と、テキストが送信されたときに呼び出される関数を受け取る `onSubmitEditing` prop があります。

たとえば、ユーザーが入力するときに、その単語を別の言語に翻訳しているとしましょう。この新しい言語では、すべての単語が同じように🍕に変換されて記述されます。したがって、"Hello there Bob"という文は「🍕 🍕 🍕」と訳されます。

```SnackPlayer name=Handling%20Text%20Input
import React, {useState} from 'react';
import {Text, TextInput, View} from 'react-native';

const PizzaTranslator = () => {
  const [text, setText] = useState('');
  return (
    <View style={{padding: 10}}>
      <TextInput
        style={{height: 40}}
        placeholder="Type here to translate!"
        onChangeText={newText => setText(newText)}
        defaultValue={text}
      />
      <Text style={{padding: 10, fontSize: 42}}>
        {text
          .split(' ')
          .map(word => word && '🍕')
          .join(' ')}
      </Text>
    </View>
  );
};

export default PizzaTranslator;
```

この例では、`text`をstateに保存します。なぜなら、state は時間とともに変化するからです。

テキスト入力でやりたいことは他にもたくさんあります。たとえば、ユーザーが入力している間に内部のテキストを検証できます。より詳細な例については、[制御されたコンポーネントに関するReactドキュメント](https://reactjs.org/docs/forms.html#controlled-components)または [TextInputのリファレンスドキュメント](textinput.md)を参照してください。

テキスト入力は、ユーザーがアプリを操作する方法の1つです。次に、別の種類の入力として[タッチの処理方法を学ぶ](handling-touches.md)を見てみましょう。
