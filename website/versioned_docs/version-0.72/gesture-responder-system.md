---
id: gesture-responder-system
title: Gesture Responder System
---

ジェスチャーレスポンダーシステムは、アプリ内のジェスチャーのライフサイクルを管理します。アプリがユーザーの意図を判断するため、タッチはいくつかの段階を経ることがあります。たとえば、アプリは、タッチがスクロールしているのか、ウィジェット上でスライドしているのか、タップしているのかを判断する必要があります。これはタッチ中に変化することもあります。同時に複数のタッチを行うこともできます。

親コンポーネントや子コンポーネントに関する追加の知識がなくても、コンポーネントがこれらのタッチインタラクションをネゴシエートできるようにするには、タッチレスポンダーシステムが必要です。

### Best Practices

アプリの使い勝手を良くするには、すべてのアクションに次の属性が必要です。

- Feedback/highlighting- タッチを処理している内容と、ジェスチャーを離したときに何が起こるかをユーザーに示します
- Cancel-ability- アクションを行うとき、ユーザーはタッチ中に指を離すことでアクションを中止できるはずです

これらの機能により、ユーザーは間違いを恐れずに実験したり操作したりできるため、ユーザーはアプリをより快適に使用できます。

### TouchableHighlight and Touchable\*

レスポンダーシステムは使い方が複雑になる場合があります。そこで、私たちは「タップ可能」であるべきもののための抽象的な `Touchable` 実装を提供しました。これはレスポンダーシステムを使用しており、タップインタラクションを宣言的に構成できます。`TouchableHighlight` は、ウェブ上のボタンやリンクを使用する場所ならどこでも使用できます。

## Responder Lifecycle

正しいネゴシエーションメソッドを実装することで、ビューをタッチレスポンダーにすることができます。ビューにレスポンダになりたいかどうかを尋ねるには、次の 2 つの方法があります。

- `View.props.onStartShouldSetResponder: evt => true,` - このビューは、タッチの開始時にレスポンダーになりたいのでしょうか？
- `View.props.onMoveShouldSetResponder: evt => true,` - View がレスポンダーでない場合、タッチの動きのたびに呼び出されます。このビューはタッチ応答性を「主張」したいのでしょうか？

View が true を返してレスポンダになろうとすると、次のいずれかになります。

- `View.props.onResponderGrant: evt => {}`-View がタッチイベントに応答するようになりました。今こそ、何が起こっているのかをユーザーに強調して示す時です
- `View.props.onResponderReject: evt => {}`-現在、他のものがレスポンダーであり、リリースしません

ビューが応答している場合は、次のハンドラーを呼び出すことができます。

- `View.props.onResponderMove: evt => {}`-ユーザーが指を動かしています
- `View.props.onResponderRelease: evt => {}`-タッチの終了時、つまり「タッチアップ」時に発生
- `View.props.onResponderTerminationRequest: evt => true`-他の何かがレスポンダーになりたがっています。このビューは回答者を解放すべきでしょうか？true を返すと、リリースが許可されます
- `View.props.onResponderTerminate: evt => {}`-レスポンダーはView から削除されました。`onResponderTerminationRequest` を呼び出した後に他のビューに表示される場合もあれば、確認せずに OS に表示される場合もあります (iOS のコントロールセンター/通知センターで発生)

`evt` は次の形式の合成タッチイベントです。

- `nativeEvent`
  - `changedTouches` - 前回のイベント以降に変更されたすべてのタッチイベントの配列
  - `identifier` - タッチの ID
  - `locationX` - エレメントを基準としたタッチの X 位置
  - `locationY` - エレメントを基準としたタッチの Y 位置
  - `pageX` - ルートエレメントを基準としたタッチの X 位置
  - `pageY` - ルートエレメントを基準としたタッチの Y 位置
  - `target` - タッチイベントを受け取るエレメントのノード ID
  - `timestamp` - タッチの時間識別子。ベロシティ計算に便利
  - `touches` - 画面上の現在のすべてのタッチの配列

### Capture ShouldSet Handlers

`onStartShouldSetResponder` と `onMoveShouldSetResponder` はバブリングパターンで呼び出され、最も深いノードが最初に呼び出されます。つまり、複数のビューが `*ShouldSetResponder` ハンドラーに true を返すと、最も深いコンポーネントがレスポンダーになります。これにより、すべてのコントロールとボタンが使用可能になるため、ほとんどの場合に適しています。

ただし、親がレスポンダーになることを確認したい場合があります。これはキャプチャフェーズを使用して処理できます。レスポンダーシステムは、最も深いコンポーネントからバブルアップする前に、キャプチャフェーズを実行して `on*ShouldSetResponderCapture` を起動します。そのため、親View がタッチスタート時に子がレスポンダにならないようにしたい場合は、true を返す `onStartShouldSetResponderCapture` ハンドラが必要です。

- `View.props.onStartShouldSetResponderCapture: evt => true,`
- `View.props.onMoveShouldSetResponderCapture: evt => true,`

### PanResponder

より高度なジェスチャー解釈については、[PanResponder](panresponder.md) をご覧ください。
