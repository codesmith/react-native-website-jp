---
id: security
title: Security
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

アプリを構築する際、セキュリティは見過ごされがちです。完全に侵入できないソフトウェアを構築することは不可能であることは事実です。完全に侵入できないロックはまだ発明されていません（結局のところ、銀行の金庫室は依然として侵入されます）。ただし、悪意のある攻撃の犠牲になったり、セキュリティの脆弱性にさらされたりする確率は、そのような不測の事態からアプリケーションを保護するために費やす労力に反比例します。普通の南京錠はピッキング可能ですが、それでもキャビネットフックよりも通り抜けるのがずっと難しいです！

<img src="/docs/assets/d_security_chart.svg" width={283} alt=" " style={{float: 'right'}} />

このガイドでは、機密情報の保存や、認証、ネットワークセキュリティ、およびアプリの保護に役立つツールに関するベストプラクティスについて説明します。これはプリフライト前のチェックリストではなく、オプションのカタログです。それぞれのオプションによって、アプリやユーザーをより強固に保護することができます。

## Storing Sensitive Info

機密性の高いAPIキーをアプリコードに保存しないでください。コードに含まれるものには、アプリバンドルを調べる人なら誰でもプレーンテキストでアクセスできます。[react-native-dotenv](https://github.com/goatandsheep/react-native-dotenv) や [react-native-config](https://github.com/luggit/react-native-config/) のようなツールは、API エンドポイントのような環境固有の変数を追加するのに最適ですが、シークレットや API キーを含むことが多いサーバー側の環境変数と混同しないでください。

アプリからリソースにアクセスするために API キーまたはシークレットが必要な場合、これを処理する最も安全な方法は、アプリとリソース間にオーケストレーションレイヤーを構築することです。これは、必要な API キーまたはシークレットを使用してリクエストを転送できるサーバーレス関数（AWS Lambda や Google Cloud Functions を使用するなど）である可能性があります。サーバーサイドコードのシークレットには、アプリコードのシークレットとは異なり、API コンシューマーがアクセスすることはできません。

**ユーザーデータを永続化する際は、機密性に基づいて適切な種類のストレージを選択してください。** アプリを使用すると、データをデバイスに保存する必要が生じることがよくあります。アプリをオフラインで使用できるようにするためだったり、ネットワークリクエストを削減するため、もしくは、セッション間でユーザーのアクセストークンを保存して、アプリを使用するたびに再認証する必要がないようにするためだったりします。

> **永続化対非永続化** — 永続化されたデータはデバイスのディスクに書き込まれるため、アプリケーションが起動するたびにアプリがデータを読み取ることができます。データを取得するために別のネットワークリクエストを行ったり、ユーザーに再入力を求めたりする必要はありません。しかし、これにより、そのデータが攻撃者によるアクセスに対してより脆弱になる可能性もあります。永続化されていないデータは決してディスクに書き込まれないため、攻撃者がアクセスする先のデータがそもそも存在しないのです。

### Async Storage

[Async Storage](https://github.com/react-native-async-storage/async-storage) は、コミュニティが管理する React Native 用のモジュールで、非同期で暗号化されていないキーバリューストアを提供します。Async Storage はアプリ間で共有されません。すべてのアプリに独自のサンドボックス環境が用意されており、他のアプリからはデータにアクセスできないようになっています。


| **以下のケースでASYNC STORAGEを使いましょう。**            | **以下のケースではASYNC STORAGEを使わないようにしましょう。** |
| --------------------------------------------- | ---------------------------------- |
| Persisting non-sensitive data across app runs | Token storage                      |
| Persisting Redux state                        | Secrets                            |
| Persisting GraphQL state                      |                                    |
| Storing global app-wide variables             |                                    |

#### Developer Notes

<Tabs groupId="guide" queryString defaultValue="web" values={constants.getDevNotesTabs(["web"])}>

<TabItem value="web">

> React Nativeにおける Async Storage は、Web における Local Storage と同等のものです。

</TabItem>
</Tabs>

### Secure Storage

React Native には、機密データを保存する手段は一切バンドルされていません。ただし、Android および iOS プラットフォームには、それぞれ既存のソリューションがあります。

#### iOS - Keychain Services

[Keychain Services](https://developer.apple.com/documentation/security/keychain_services) を使用すると、ユーザーの複数の機密情報を安全に保存できます。これは、機密情報を保存するのに理想的な場所です。Async Storage に入れるべきではない、証明書や、トークン、パスワード、および その他の機密情報を保存します。

#### Android - Secure Shared Preferences

[Shared Preferences](https://developer.android.com/reference/android/content/SharedPreferences) は Android の永続的なキーバリューデータストアに相当します。**Shared Preferencesのデータはデフォルトでは暗号化されません**。ただし、[Encrypted Shared Preferences](https://developer.android.com/topic/security/data) は Android のShared Preferencesクラスをラップし、キーと値を自動的に暗号化します。

#### Android - Keystore

[Android Keystore](https://developer.android.com/training/articles/keystore) システムでは、暗号化キーをコンテナに保存して、デバイスからの抽出をより困難にすることができます。

iOS Keychain サービス または Android Secure Shared Preferences を使用するには、自分でブリッジを作成するか、それらをラップして統一された API を提供するライブラリを自己の責任で使用するか、です。検討すべきライブラリは以下の通りです。

- [expo-secure-store](https://docs.expo.dev/versions/latest/sdk/securestore/)
- [react-native-encrypted-storage](https://github.com/emeraldsanto/react-native-encrypted-storage) - iOS ではKeychain、Android ではEncryptedSharedPreferencesを使用します。
- [react-native-keychain](https://github.com/oblador/react-native-keychain)
- [react-native-sensitive-info](https://github.com/mCodex/react-native-sensitive-info) - iOS の場合はセキュアですが、Android では（デフォルトではセキュリティで保護されていない）Shared Preferencesを使用します。ただし、Android キーストアを使用する [ブランチ](https://github.com/mCodex/react-native-sensitive-info/tree/keystore) もあります。
  - [redux-persist-sensitive-storage](https://github.com/CodingZeal/redux-persist-sensitive-storage) - Reduxのreact-native-sensitive-infoをラップしたものです。

> **意図せずに機密情報を保存したり公開したりしないように注意してください。** これは、偶発的に発生する可能性があります。たとえば、機密フォームデータを redux state で保存したり、state ツリー全体を Async Storage に保持することはしないでください。また、ユーザートークンと個人情報をSentryやCrashlyticsなどのアプリケーション監視サービスに送信することもしないでください。

## Authentication and Deep Linking

<img src="/docs/assets/d_security_deep-linking.svg" width={225} alt=" " style={{float: 'right', margin: '0 0 1em 1em'}} />

モバイルアプリには、ウェブには存在しない独特の脆弱性があります：**ディープリンク**です。ディープリンクは、外部ソースからネイティブアプリケーションに直接データを送信する方法です。ディープリンクは `app://` のようになります。ここで、`app`はあなたのアプリのアプリスキームで、//に続くものはすべて内部でリクエストを処理するために使用できます。

たとえば、eコマースアプリを構築している場合、`app://products/1`を使用してあなたのアプリにディープリンクを張り、ID 1の商品の商品詳細ページを開くことができます。ウェブではこのようなURLを考えることができますが、重要な違いが1つあります。

ディープリンクは安全ではないので、機密情報を決して送信しないでください。

ディープリンクが安全でない理由は、URLスキームを一元的に登録する方法がないからです。アプリケーション開発者は、iOSの場合は [Xcodeで設定する](https://developer.apple.com/documentation/uikit/inter-process_communication/allowing_apps_and_websites_to_link_to_your_content/defining_a_custom_url_scheme_for_your_app)、[Androidでインテントを追加する](https://developer.android.com/training/app-links/deep-linking) など、選択するほとんどすべてのURLスキームを使用できます。

同じスキームに登録して、リンクに含まれるデータにアクセスすることで、悪意のあるアプリケーションがディープリンクを乗っ取るのを防ぐことはできません。`app://products/1`のようなものを送信しても害はありませんが、トークンを送信することはセキュリティ上の懸念事項になります。

オペレーティングシステムにリンクを開くときに選択できるアプリケーションが2つ以上ある場合、Androidはユーザーに [曖昧性回避ダイアログ](https://developer.android.com/training/basics/intents/sending#disambiguation-dialog) を表示して、リンクを開くために使用するアプリケーションを選択するように求めます。しかしiOSでは、オペレーティングシステムが自動的に選択するので、ユーザーは幸いにも気付かないでしょう。Appleは、先着順の原則を制定した後のiOSバージョン（iOS 11）でこの問題に対処するための措置を講じてきました。ただし、この脆弱性は依然としてさまざまな方法で悪用される可能性があります。詳細については、[こちら](https://thehackernews.com/2019/07/ios-custom-url-scheme.html)をご覧ください。[ユニバーサルリンク](https://developer.apple.com/ios/universal-links/) を使用すると、iOSでアプリ内のコンテンツに安全にリンクできます。

### OAuth2 and Redirects

OAuth2認証プロトコルは、最近非常に人気があり、最も完全で安全なプロトコルとして喧伝されています。OpenID Connectプロトコルもこれに基づいています。OAuth2では、ユーザーは第三者を介した認証を求められます。正常に完了すると、この第三者はJWT（[JSON Webトークン](https://jwt.io/introduction/)）と交換できる確認コードを使用して、要求元のアプリケーションにリダイレクトします。JWTは、ウェブ上の当事者間で情報を安全に送信するためのオープンスタンダードです。

ウェブでは、このリダイレクト手順は安全です。なぜなら、ウェブ上のURLは一意であることが保証されているからです。先に述べたように、URLスキームを一元的に登録する方法がないため、これはアプリには当てはまりません！このセキュリティ上の問題に対処するには、PKCEという形でチェックを追加する必要があります。

[PKCE](https://oauth.net/2/pkce/) は、「Pixy」と発音します。`Proof of Key Code Exchange(キーコード交換の証明)`の略で、OAuth 2仕様の拡張です。これには、認証とトークン交換の要求が同じクライアントからのものであることを確認するセキュリティ層を追加することが含まれます。PKCEは [SHA 256](https://www.movable-type.co.uk/scripts/sha256.html) 暗号学的ハッシュ関数を使用しています。SHA 256は、任意のサイズのテキストまたはファイルに固有の「署名」を作成しますが、次のようになります。

- 入力ファイルに関係なく常に同じ長さです
- 同じ入力に対して常に同じ結果が得られることが保証されています
- 1本道（つまり、リバースエンジニアリングして元の入力を表示することはできません）

さて、あなたには2つの値があります：

- **code_verifier** - クライアントによって生成される大きなランダムな文字列
- **code_challenge** - コードベリファイアのSHA 256

最初の「/authorize」リクエスト中に、クライアントはメモリに保存している「code_verifier」に対応する「code_challenge」も送信します。承認リクエストが正しく返された後、クライアントは「code_challenge」を生成するために使用した「code_verifier」も送信します。次に、IDPは「code_challenge」を計算し、最初の「/authorize」リクエストで設定されたものと一致するかどうかを確認し、値が一致する場合にのみアクセストークンを送信します。

これにより、最初のauthorization flowをトリガーしたアプリケーションだけが検証コードをJWTと正常に交換できることが保証されます。そのため、悪意のあるアプリケーションが確認コードにアクセスしても、それだけでは役に立ちません。これを実際に見るには、[この例](https://aaronparecki.com/oauth-2-simplified/#mobile-apps) をチェックしてください。

ネイティブ OAuth について検討すべきライブラリは [react-native-app-auth](https://github.com/FormidableLabs/react-native-app-auth) です。react-native-app-auth は、OAuth2プロバイダーと通信するためのSDKです。ネイティブの [AppAuth-iOS](https://github.com/openid/AppAuth-iOS) と [AppAuth-Android](https://github.com/openid/AppAuth-Android) ライブラリをラップし、PKCEをサポートできます。

> react-native-app-authは、アイデンティティプロバイダがサポートしている場合にのみPKCEをサポートできます。

![OAuth2 with PKCE](/docs/assets/diagram_pkce.svg)

## Network Security

APIは常に [SSL暗号化](https://www.ssl.com/faqs/faq-what-is-ssl/) を使用する必要があります。SSL暗号化は、要求されたデータがサーバーを離れてからクライアントに届くまでの間にプレーンテキストで読み取られるのを防ぎます。エンドポイントは「http://」ではなく「https://」で始まるので、エンドポイントが安全であることがわかります。

### SSL Pinning

httpsエンドポイントを使用しても、まだデータが傍受されやすくなる可能性が残っています。httpsでは、クライアントは、クライアントにあらかじめインストールされている信頼できる認証局によって署名された有効な証明書を提供できる場合にのみサーバーを信頼します。攻撃者はこれを利用して、悪意のあるルートCA証明書をユーザーのデバイスにインストールして、クライアントが攻撃者が署名したすべての証明書を信頼する可能性があります。したがって、証明書だけに頼っていると、[中間者攻撃](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)に対して脆弱なままになる可能性があります。

**SSLピンニング**は、この攻撃を回避するためにクライアント側で使用できる手法です。開発中に信頼できる証明書のリストをクライアントに埋め込む（またはピン留めする）ことで機能します。そうすれば、信頼できる証明書の1つで署名されたリクエストだけが受け入れられ、自己署名証明書は受け付けられません。

> SSLピンニングを使用するときは、証明書の有効期限に注意する必要があります。証明書は1〜2年ごとに期限切れになり、有効期限が切れると、サーバーだけでなくアプリでも更新する必要があります。サーバー上の証明書が更新されるとすぐに、古い証明書が埋め込まれているアプリはすべて機能しなくなります。

## Summary

セキュリティに対処するための万能な方法はありませんが、意識的な努力と勤勉により、アプリケーションのセキュリティ侵害の可能性を大幅に減らすことができます。アプリケーションに保存されているデータの機密性、ユーザー数、ハッカーがアカウントにアクセスしたときに与える可能性のある損害に比例した投資をセキュリティに対して行ってください。また、覚えておいてください：そもそも要求されたことのない情報にアクセスするのは非常に難しいです。
