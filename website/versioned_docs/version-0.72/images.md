---
id: images
title: Images
---

## Static Image Resources

React Native は、AndroidアプリとiOSアプリの画像やその他のメディアアセットを一元的に管理する方法を提供します。静止画像をアプリに追加するには、ソースコードツリーのどこかに配置し、次のように参照します。

```tsx
<Image source={require('./my-icon.png')} />
```

イメージ名は JS モジュールが解決されるのと同じ方法で解決されます。上の例では、バンドラーはそれを必要とするコンポーネントと同じフォルダーで `my-icon.png` を探します。

`@2x` と `@3x` のサフィックスを使用して、さまざまな画面密度の画像を提供できます。次のファイル構造がある場合:

```
.
├── button.js
└── img
    ├── check.png
    ├── check@2x.png
    └── check@3x.png
```

... と `button.js` コードには以下が含まれます。

```tsx
<Image source={require('./img/check.png')} />
```

... バンドラーは、デバイスの画面密度に対応する画像をバンドルして提供します。たとえば、`check@2x.png` は iPhone 7 で使用され、`check@3x.png` は iPhone 7 Plus または Nexus 5 で使用されます。画面の密度に一致する画像がない場合は、最も近い最適なオプションが選択されます。

Windows では、プロジェクトに新しいイメージを追加する場合、バンドラーの再起動が必要になる場合があります。

次のようなメリットがあります。

1. Android とiOSで同じシステム。
2. 画像はJavaScriptコードと同じフォルダーにあります。コンポーネントは自己完結型です。
3. グローバル名前空間はありません。つまり、名前の衝突を心配する必要はありません。
4. 実際に使用された画像のみがアプリにパッケージ化されます。
5. 画像を追加したり変更したりする場合、アプリを再コンパイルする必要はありません。通常どおりシミュレーターを更新できます。
6. バンドラーは画像のサイズを知っているので、コード内で複製する必要はありません。
7。画像は [npm](https://www.npmjs.com/) パッケージで配布できます。

これが機能するためには、`require` の画像名が静的にわかっている必要があります。

```tsx
// GOOD
<Image source={require('./my-icon.png')} />;

// BAD
const icon = this.props.active
  ? 'my-icon-active'
  : 'my-icon-inactive';
<Image source={require('./' + icon + '.png')} />;

// GOOD
const icon = this.props.active
  ? require('./my-icon-active.png')
  : require('./my-icon-inactive.png');
<Image source={icon} />;
```

この方法に必要な image ソースには、Image のサイズ (width、height) 情報が含まれていることに注意してください。画像を動的に (例えば flex 経由で) 拡大縮小する必要がある場合は、style 属性に手動で `{width: undefined, height: undefined}` を設定する必要があるかもしれません。

## Static Non-Image Resources

上記の `require` 構文を使用して、オーディオ、ビデオ、またはドキュメントファイルをプロジェクトに静的に含めることもできます。`.mp3`、`.wav`、`.mp4`、`.mov`、`.html`、`.pdf` など、最も一般的なファイルタイプがサポートされています。全リストは [bundler defaults](https://github.com/facebook/metro/blob/master/packages/metro-config/src/defaults/defaults.js#L14-L44) をご覧ください。

[Metro configuration](https://facebook.github.io/metro/docs/configuration) に [`assetExts` resolver option](https://facebook.github.io/metro/docs/configuration#resolver-options) を追加することで、他のタイプのサポートを追加できます。

注意すべき点は、現在、画像以外のアセットにはサイズ情報が渡されないため、動画では `flexGrow` ではなく絶対位置を使用する必要があるということです。この制限は、Xcode または Android 用の Assets フォルダーに直接リンクされている動画には適用されません。

## Images From Hybrid App's Resources

（一部のUIをReact Nativeで、一部のUIをプラットフォームコードで）ハイブリッドアプリを構築している場合でも、アプリにすでにバンドルされているイメージを使用できます。

Xcode アセットカタログまたは Android drawable フォルダーに含まれる画像には、拡張子のない画像名を使用してください。

```tsx
<Image
  source={{uri: 'app_icon'}}
  style={{width: 40, height: 40}}
/>
```

Android アセットフォルダー内の画像には、`asset:/` スキームを使用してください。

```tsx
<Image
  source={{uri: 'asset:/app_icon.png'}}
  style={{width: 40, height: 40}}
/>
```

これらのアプローチでは安全性チェックは行われません。これらの画像がアプリケーションで利用可能であることを保証するかどうかはあなた次第です。また、画像のサイズを手動で指定する必要があります。

## Network Images

アプリに表示する画像の多くは、コンパイル時には使用できません。または、バイナリサイズを小さく保つために動的に読み込む必要があります。静的リソースとは異なり、_画像のサイズを手動で指定する必要があります_。iOS の [App Transport Security](publishing-to-app-store.md#1-enable-app-transport-security) 要件を満たすには、https も使用することを強くお勧めします。

```tsx
// GOOD
<Image source={{uri: 'https://reactjs.org/logo-og.png'}}
       style={{width: 400, height: 400}} />

// BAD
<Image source={{uri: 'https://reactjs.org/logo-og.png'}} />
```

### Network Requests for Images

イメージリクエストと一緒にHTTP-Verb、Headers、Bodyなどを設定したい場合は、ソースオブジェクトに次のプロパティを定義することで設定できます。

```tsx
<Image
  source={{
    uri: 'https://reactjs.org/logo-og.png',
    method: 'POST',
    headers: {
      Pragma: 'no-cache',
    },
    body: 'Your Body goes here',
  }}
  style={{width: 400, height: 400}}
/>
```

## Uri Data Images

場合によっては、REST API 呼び出しからエンコードされた画像データを取得することがあります。`'data:'` uri スキームを使用してこれらの画像を使用できます。ネットワークリソースの場合と同様に、_画像のサイズを手動で指定する必要があります_。

:::info
これは、データベースのリストにあるアイコンなど、非常に小さくて動的な画像にのみ推奨されます。
:::

```tsx
// include at least width and height!
<Image
  style={{
    width: 51,
    height: 51,
    resizeMode: 'contain',
  }}
  source={{
    uri: 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADMAAAAzCAYAAAA6oTAqAAAAEXRFWHRTb2Z0d2FyZQBwbmdjcnVzaEB1SfMAAABQSURBVGje7dSxCQBACARB+2/ab8BEeQNhFi6WSYzYLYudDQYGBgYGBgYGBgYGBgYGBgZmcvDqYGBgmhivGQYGBgYGBgYGBgYGBgYGBgbmQw+P/eMrC5UTVAAAAABJRU5ErkJggg==',
  }}
/>
```

### Cache Control (iOS Only)

場合によっては、画像が既にローカル キャッシュにある場合にのみ、つまり、高解像度が利用可能になるまで低解像度のプレースホルダーのみを表示したい場合があります。それ以外の場合は、画像が古くなっていても気にせず、帯域幅を節約するために古い画像を表示しても構わない場合があります。`cache` source プロパティでは、ネットワークレイヤーがキャッシュとどのように相互作用するかを制御できます。

- `default`: ネイティブプラットフォームのデフォルト戦略を使用してください。
- `reload`: URL のデータは元のソースからロードされます。URL ロードリクエストを満たすために既存のキャッシュデータを使用しないでください。
- `force-cache`: 保存期間や有効期限に関係なく、既存のキャッシュデータを使用してリクエストに対応します。リクエストに対応する既存のデータがキャッシュに存在しない場合、データは元のソースからロードされます。
- `only-if-cached`: 既存のキャッシュデータは、その age や有効期限日に関係なく、リクエストを満たすために使用されます。URL ロードリクエストに対応する既存のデータがキャッシュに存在しない場合、元のソースからデータをロードしようとはせず、ロードは失敗したと見なされます。

```tsx
<Image
  source={{
    uri: 'https://reactjs.org/logo-og.png',
    cache: 'only-if-cached',
  }}
  style={{width: 400, height: 400}}
/>
```

## Local Filesystem Images

`Images.xcassets` 以外のローカルリソースの使用例については、[CameraRoll](https://github.com/react-native-community/react-native-cameraroll) を参照してください。

### Best Camera Roll Image

iOSはカメラロールの中で、同じ画像に対して複数のサイズを保存します。パフォーマンス上の理由から、できるだけ近いサイズを選択することが非常に重要です。200 x 200 のサムネイルを表示するときに、フル画質の 3264x2448 の画像をソースとして使用することは望ましくありません。完全に一致する場合、React Native はそれを選択します。そうでない場合は、近いサイズからサイズを変更するときにぼやけないように、少なくとも50％大きい最初のものを使用します。これらはすべてデフォルトで実行されるため、面倒な (そしてエラーが発生しやすい) コードを自分で作成する必要はありません。

## Why Not Automatically Size Everything?

_ブラウザでは、_ 画像にサイズを指定しない場合、ブラウザは0x0要素をレンダリングし、画像をダウンロードしてから、正しいサイズに基づいて画像をレンダリングします。この動作の大きな問題は、画像が読み込まれるとUIがあちこちに飛び移り、ユーザーエクスペリエンスが非常に悪くなることです。

_React Nativeでは、_ この動作は意図的に実装されていません。開発者がリモート画像のサイズ（または縦横比）を事前に知っておくことは手間がかかりますが、ユーザーエクスペリエンスの向上につながると考えています。`require('./my-icon.png')` 構文を使用してアプリバンドルから読み込まれた静止画像は、マウント時にその寸法がすぐにわかるため、_自動的にサイズを変更_ できます。

たとえば、`require('./my-icon.png')` の結果は次のようになります。

```tsx
{"__packager_asset":true,"uri":"my-icon.png","width":591,"height":573}
```

## Source as an object

React Native での興味深い決定の 1 つは、`src` 属性が `source` という名前で、文字列ではなく `uri` 属性を持つオブジェクトを受け取るということです。

```tsx
<Image source={{uri: 'something.jpg'}} />
```

インフラストラクチャ側の理由は、このオブジェクトにメタデータを添付できるためです。たとえば、`require('./my-icon.png')` を使用している場合は、実際の場所とサイズに関する情報を追加します (この事実は将来変更される可能性があるため、この事実を当てにしないでください!)。これは将来を見据えたものでもあります。たとえば、ある時点でスプライトをサポートしたい場合があります。`{uri: ...}` を出力する代わりに `{uri: ..., crop: {left: 10, top: 50, width: 20, height: 40}}` を出力して、既存のすべての呼び出しサイトでスプライトを透過的にサポートできます。

ユーザー側では、これにより、画像のサイズなどの便利な属性を使用してオブジェクトに注釈を付け、表示されるサイズを計算できます。画像に関するより多くの情報を保存するためのデータ構造として自由に使用してください。

## Background Image via Nesting

ウェブに詳しいデベロッパーからよく寄せられる機能リクエストは `background-image` です。このユースケースに対応するには、`<Image>` と同じプロパティを持つ `<ImageBackground>` コンポーネントを使用し、その上に重ねたい子コンポーネントを追加します。

`<ImageBackground>` の実装は基本的なものなので、場合によっては使いたくないかもしれません。詳細については `<ImageBackground>` の [documentation](imagebackground.md) を参照し、必要に応じて独自のカスタムコンポーネントを作成してください。

```tsx
return (
  <ImageBackground source={...} style={{width: '100%', height: '100%'}}>
    <Text>Inside</Text>
  </ImageBackground>
);
```

一部の幅と高さのスタイル属性を指定する必要があることに注意してください。

## iOS Border Radius Styles

以下のコーナー固有のボーダー半径スタイルプロパティは、iOSの画像コンポーネントでは無視される場合があることに注意してください。

- `borderTopLeftRadius`
- `borderTopRightRadius`
- `borderBottomLeftRadius`
- `borderBottomRightRadius`

## Off-thread Decoding

Image デコードには、1 フレーム分以上の時間がかかる場合があります。デコードはメインスレッドで行われるため、これはウェブ上のフレームドロップの主な原因の1つです。React Native では、画像のデコードは別のスレッドで行われます。実際には、画像がまだダウンロードされていない場合に対処する必要があるため、デコード中にプレースホルダーをさらに数フレーム表示しても、コードを変更する必要はありません。

## Configuring iOS Image Cache Limits

iOSでは、React Nativeのデフォルトの画像キャッシュ制限を無効にするAPIを公開しています。これはネイティブの AppDelegate コード内 (例:`didFinishLaunchingWithOptions` 内) から呼び出す必要があります。

```objectivec
RCTSetImageCacheLimits(4*1024*1024, 200*1024*1024);
```

**Parameters:**

| Name           | Type   | Required | Description             |
| -------------- | ------ | -------- | ----------------------- |
| imageSizeLimit | number | Yes      | Image cache size limit. |
| totalCostLimit | number | Yes      | Total cache cost limit. |

上記のコード例では、画像サイズの上限は 4 MB に設定され、合計コスト制限は 200 MB に設定されています。
