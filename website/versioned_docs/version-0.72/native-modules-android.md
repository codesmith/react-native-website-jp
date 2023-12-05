---
id: native-modules-android
title: Android Native Modules
---

import NativeDeprecated from './the-new-architecture/\_markdown_native_deprecation.mdx'
import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<NativeDeprecated />

Android用ネイティブモジュールへようこそ。まずは、[Native Modules Intro](native-modules-intro) を読んで、ネイティブモジュールの基本を理解してください。

## Create a Calendar Native Module

次のガイドでは、ネイティブモジュール`CalendarModule`を作成します。JavaScriptからAndroidのカレンダーAPIにアクセスできるようにするネイティブモジュールです。最終的に、JavaScriptから `CalendarModule.createCalendarEvent('Dinner Party', 'My House');`をコールして、カレンダーイベントを作成する Java/Kotlin メソッドを呼び出すことができるようになります。

### Setup

はじめに、Android StudioのReact Native アプリケーション内でAndroidプロジェクトを開いてください。Androidプロジェクトは、React Native アプリ内の下の画像のところあたりで見つけられます。：

<figure>
  <img src="/docs/assets/native-modules-android-open-project.png" width="500" alt="Image of opening up an Android project within a React Native app inside of Android Studio." />
  <figcaption>Image of where you can find your Android project</figcaption>
</figure>

ネイティブコードを書くにはAndroid Studioを使うことをお勧めします。Android StudioはAndroid開発用にビルドされたIDEで、これを使用するとコード構文エラーなどの些細な問題をすばやく解決することができます。

また、[Gradle Daemon](https://docs.gradle.org/2.9/userguide/gradle_daemon.html) を有効にして、Java/Kotlinコードを反復処理するときのビルドをスピードアップすることをお勧めします。

### Create A Custom Native Module File

最初のステップは、`android/app/src/main/java/com/your-app-name/`フォルダ内に (`CalendarModule.java` もしくは `CalendarModule.kt`) というJava/Kotlinファイルを作成することです（このフォルダはKotlinとJavaの両方で同じです）。このJava/Kotlinファイルには、ネイティブモジュールのJava/Kotlinクラスが含まれます。

<figure>
  <img src="/docs/assets/native-modules-android-add-class.png" width="700" alt="Image of adding a class called CalendarModule.java within the Android Studio." />
  <figcaption>Image of how to add the CalendarModuleClass</figcaption>
</figure>

次に、以下の内容を追記してください。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
package com.your-apps-package-name; // replace your-apps-package-name with your app’s package name
import com.facebook.react.bridge.NativeModule;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.bridge.ReactContext;
import com.facebook.react.bridge.ReactContextBaseJavaModule;
import com.facebook.react.bridge.ReactMethod;
import java.util.Map;
import java.util.HashMap;

public class CalendarModule extends ReactContextBaseJavaModule {
   CalendarModule(ReactApplicationContext context) {
       super(context);
   }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
package com.your-apps-package-name; // replace your-apps-package-name with your app’s package name
import com.facebook.react.bridge.NativeModule
import com.facebook.react.bridge.ReactApplicationContext
import com.facebook.react.bridge.ReactContext
import com.facebook.react.bridge.ReactContextBaseJavaModule
import com.facebook.react.bridge.ReactMethod

class CalendarModule(reactContext: ReactApplicationContext) : ReactContextBaseJavaModule(reactContext) {...}
```

</TabItem>
</Tabs>

ご覧のとおり、あなたの「CalendarModule」クラスは「ReactContextBaseJavaModule」クラスを拡張しています。Androidの場合、Java/Kotlinのネイティブモジュールは、「ReactContextBaseJavaModule」を拡張し、JavaScriptに必要な機能を実装するクラスとして記述されています。

> Java/KotlinクラスがReact Native によってネイティブモジュールと見なされるためには、技術的には「BaseJavaModule」クラスを拡張するか、「NativeModule」インターフェイスを実装するだけでよいということは注目に値します。

> ただし、上記のように「ReactContextBaseJavaModule」を使用することをお勧めします。「ReactContextBaseJavaModule」は、「ReactApplicationContext（RAC）」へのアクセスを提供します。これは、アクティビティライフサイクルメソッドにフックする必要があるネイティブモジュールに役立ちます。「ReactContextBaseJavaModule」を使用すると、将来、ネイティブモジュールを型安全にするのも簡単になります。将来のリリースで予定されているネイティブモジュールの型安全のために、React Native は各ネイティブモジュールのJavaScript仕様を見て、「ReactContextBaseJavaModule」を拡張する抽象基底クラスを生成します。

### Module Name

AndroidのすべてのJava/Kotlinネイティブモジュールは、`getName()`メソッドを実装する必要があります。このメソッドは、ネイティブモジュールの名前を表す文字列を返します。ネイティブモジュールには、その名前を使ってJavaScriptでアクセスできます。たとえば、以下のコードスニペットでは、`getName()`は`"CalendarModule"`を返します。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
// add to CalendarModule.java
@Override
public String getName() {
   return "CalendarModule";
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
// add to CalendarModule.kt
override fun getName() = "CalendarModule"
```

</TabItem>
</Tabs>

ネイティブモジュールには、JSで次のようにアクセスできます。

```tsx
const {CalendarModule} = ReactNative.NativeModules;
```

### Export a Native Method to JavaScript

次に、カレンダーイベントを作成し、JavaScriptで呼び出すことができるメソッドをネイティブモジュールに追加する必要があります。JavaScriptから呼び出されるすべてのネイティブモジュールメソッドには、`@ReactMethod`というアノテーションを付ける必要があります。

JSでは`CalendarModule.createCalendarEvent()`を介して呼び出すことができる「CalendarModule」の「CreateCalendarEvent()」メソッドを設定します。今のところ、メソッドは名前と場所を文字列として受け取ります。引数の型オプションについては後ほど少し説明します。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
@ReactMethod
public void createCalendarEvent(String name, String location) {
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
@ReactMethod fun createCalendarEvent(name: String, location: String) {}
```

</TabItem>
</Tabs>

メソッドにデバッグログを追加して、アプリケーションから呼び出したときに呼び出されていることを確認します。以下は、Android utilパッケージから [Log](https://developer.android.com/reference/android/util/Log) クラスをインポートして使用する方法の例です。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
import android.util.Log;

@ReactMethod
public void createCalendarEvent(String name, String location) {
   Log.d("CalendarModule", "Create event called with name: " + name
   + " and location: " + location);
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
import android.util.Log

@ReactMethod
fun createCalendarEvent(name: String, location: String) {
    Log.d("CalendarModule", "Create event called with name: $name and location: $location")
}
```

</TabItem>
</Tabs>

ネイティブモジュールの実装を完了してJavaScriptに接続したら、[次の手順](https://developer.android.com/studio/debug/am-logcat.html) に従ってアプリのログを表示できます。

### Synchronous Methods

`isBlockingSynchronousMethod = true` をネイティブメソッドに渡して、同期メソッドとしてマークすることができます。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
@ReactMethod(isBlockingSynchronousMethod = true)
```

</TabItem>
<TabItem value="kotlin">

```kotlin
@ReactMethod(isBlockingSynchronousMethod = true)
```

</TabItem>
</Tabs>

現時点では、この方法はお勧めしません。メソッドを同期的に呼び出すと、パフォーマンスが大幅に低下し、ネイティブモジュールにスレッド関連のバグが発生する可能性があるためです。また、「isBlockingSynchronousMethod」を有効にすると、アプリはGoogle Chromeデバッガーを使用できなくなることにも注意してください。これは、同期メソッドがJS VMにアプリとメモリを共有するように要求するためです。Google Chromeデバッガーの場合、React Native はGoogle Chrome のJS VM内で実行され、WebSockets を介してモバイルデバイスと非同期的に通信します。

### Register the Module (Android Specific)

ネイティブモジュールを作成したら、React Native に登録する必要があります。そのためには、ネイティブモジュールを「ReactPackage」に追加し、「ReactPackage」をReact Native に登録する必要があります。初期化中、React Native はすべてのパッケージをループし、「ReactPackage」ごとに、各ネイティブモジュールをその中に登録します。

React Nativeは、登録するネイティブモジュールのリストを取得するために、`ReactPackage`の`createNativeModules()`メソッドを呼び出します。Androidの場合、モジュールがインスタンス化されてcreateNativeModulesで返却されない場合、そのモジュールはJavaScriptからは使用できません。

ネイティブモジュールを「ReactPackage」に追加するには、まず、`android/app/src/main/java/com/your-app-name/`フォルダ内に「ReactPackage」を実装する（`MyAppPackage.java` または `MyAppPackage.kt`）という名前の新しいJava/Kotlinクラスを作成します。

次に、以下の内容を追加します。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
package com.your-app-name; // replace your-app-name with your app’s name
import com.facebook.react.ReactPackage;
import com.facebook.react.bridge.NativeModule;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.uimanager.ViewManager;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class MyAppPackage implements ReactPackage {

   @Override
   public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
       return Collections.emptyList();
   }

   @Override
   public List<NativeModule> createNativeModules(
           ReactApplicationContext reactContext) {
       List<NativeModule> modules = new ArrayList<>();

       modules.add(new CalendarModule(reactContext));

       return modules;
   }

}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
package com.your-app-name // replace your-app-name with your app’s name

import android.view.View
import com.facebook.react.ReactPackage
import com.facebook.react.bridge.NativeModule
import com.facebook.react.bridge.ReactApplicationContext
import com.facebook.react.uimanager.ReactShadowNode
import com.facebook.react.uimanager.ViewManager

class MyAppPackage : ReactPackage {

    override fun createViewManagers(
        reactContext: ReactApplicationContext
    ): MutableList<ViewManager<View, ReactShadowNode<*>>> = mutableListOf()

    override fun createNativeModules(
        reactContext: ReactApplicationContext
    ): MutableList<NativeModule> = listOf(CalendarModule(reactContext)).toMutableList()
}
```

</TabItem>
</Tabs>

このファイルは、あなたが作成したネイティブモジュールである「CalendarModule」をインポートします。次に、「createNativeModules()」関数内で「CalendarModule」をインスタンス化し、それを「NativeModules」のリストとして返却し、登録します。後でさらにネイティブモジュールを追加する場合も、それらをインスタンス化して、ここで返却されるリストに追加することもできます。

> ネイティブモジュールを登録するこの方法では、アプリケーションの起動時にすべてのネイティブモジュールを強制的に初期化するので、アプリケーションの起動時間が長くなることに注意してください。代わりに [TurboReactPackage](https://github.com/facebook/react-native/blob/main/packages/react-native/ReactAndroid/src/main/java/com/facebook/react/TurboReactPackage.java) を使うこともできます。インスタンス化されたネイティブモジュールオブジェクトのリストを返す「createNativeModules」の代替として、TurboReactPackageは必要に応じてネイティブモジュールオブジェクトを作成する「getModule(String name, ReactApplicationContext rac)」メソッドを実装しています。TurboReactPackageは、現時点で実装するのが少し複雑です。「getModule()」メソッドを実装することに加えて、「getReactModuleInfoProvider()」メソッドを実装する必要があります。このメソッドは、パッケージがインスタンス化できるすべてのネイティブモジュールのリストと、それらをインスタンス化する関数（例 [here](https://github.com/facebook/react-native/blob/8ac467c51b94c82d81930b4802b2978c85539925/ReactAndroid/src/main/java/com/facebook/react/CoreModulesPackage.java#L86-L165)）を返却します。繰り返しますが、TurboReactPackageを使用すると、アプリケーションの起動時間を短縮できますが、現時点では書くのが少し面倒です。そのため、TurboReactパッケージを使用する場合は注意して進めてください。

「CalendarModule」パッケージを登録するには、ReactNativeHostの「getPackages()」メソッドで返却されるパッケージのリストに「MyAppPackage」を追加する必要があります。「MainApplication.java」または「MainApplication.kt」ファイルを開きます。このファイルは、`android/app/src/main/java/com/your-app-name/`というパスにあります。

ReactNativeHostの `getPackages()`メソッドを探して、パッケージを`getPackages() `が返却するパッケージリストに追加してください。：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
@Override
  protected List<ReactPackage> getPackages() {
    @SuppressWarnings("UnnecessaryLocalVariable")
    List<ReactPackage> packages = new PackageList(this).getPackages();
    // below MyAppPackage is added to the list of packages returned
    packages.add(new MyAppPackage());
    return packages;
  }
```

</TabItem>
<TabItem value="kotlin">

```kotlin
override fun getPackages(): List<ReactPackage> =
    PackageList(this).packages.apply {
        // Packages that cannot be autolinked yet can be added manually here, for example:
        // packages.add(new MyReactNativePackage());
        add(MyAppPackage())
    }
```

</TabItem>
</Tabs>

これで、Android用のネイティブモジュールが正常に登録されました！

### Test What You Have Built

この時点で、Androidのネイティブモジュールの基本的な足場を設定できました。ネイティブモジュールにアクセスし、エクスポートされたメソッドをJavaScriptで呼び出してテストしてください。

ネイティブモジュールの `createCalendarEvent()`メソッドへの呼び出しを追加したいアプリケーション内の場所を見つけてください。以下は、アプリに追加できるコンポーネント「NewModuleButton」の例です。「NewModuleButton」の `onPress()`関数内でネイティブモジュールを呼び出すことができます。

```tsx
import React from 'react';
import {NativeModules, Button} from 'react-native';

const NewModuleButton = () => {
  const onPress = () => {
    console.log('We will invoke the native module here!');
  };

  return (
    <Button
      title="Click to invoke your native module!"
      color="#841584"
      onPress={onPress}
    />
  );
};

export default NewModuleButton;
```

JavaScriptからネイティブモジュールにアクセスするには、まずReact Native から `NativeModules`をインポートする必要があります。

```tsx
import {NativeModules} from 'react-native';
```

これで、`NativeModules`から`CalendarModule`ネイティブモジュールにアクセスできます。

```tsx
const {CalendarModule} = NativeModules;
```

これで、CalendarModule ネイティブモジュールが利用可能になったので、ネイティブメソッド `createCalendarEvent()`を呼び出すことができます。以下のように、`NewModuleButton`の `onPress()`メソッドに追加されています。

```tsx
const onPress = () => {
  CalendarModule.createCalendarEvent('testName', 'testLocation');
};
```

最後のステップは、React Native アプリをリビルドして、最新のネイティブコードを（新しいネイティブモジュールで！）使用できるようにすることです。React Nativeアプリケーションがあるコマンドラインで、次のコマンドを実行します。

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run android
```

</TabItem>
<TabItem value="yarn">

```shell
yarn android
```

</TabItem>
</Tabs>

### Building as You Iterate

これらのガイドを読み、ネイティブモジュールを繰り返し使用する場合、JavaScriptからの最新の変更にアクセスするには、アプリケーションをネイティブにリビルドする必要があります。これは、あなたが書いているコードがアプリケーションのネイティブ部分にあるからです。React Nativeのメトロバンドラーは、JavaScriptの変更を監視してその場でリビルドできますが、ネイティブコードの場合はそうしません。したがって、最新のネイティブ変更をテストしたい場合は、上記のコマンドを使用してリビルドする必要があります。

### Recap✨

これで、アプリのネイティブモジュール機能で `createCalendarEvent()`メソッドを呼び出すことができるはずです。この例では、この呼び出し処理は「NewModuleButton」を押すことで実行されます。このことを確認するには、`createCalendaEvent()`メソッドで設定したログを見てください。[これらのステップ](https://developer.android.com/studio/debug/am-logcat.html) に従うと、アプリの ADB ログを見ることができます。そうすれば、「Log.d」メッセージ（この例では“Create event called with name: testName and location: testLocation”）を検索して、ネイティブモジュールメソッドを呼び出すたびにメッセージが記録されるのを確認できるはずです。

<figure>
  <img src="/docs/assets/native-modules-android-logs.png" width="1000" alt="Image of logs." />
  <figcaption>Image of ADB logs in Android Studio</figcaption>
</figure>

この時点で、Androidネイティブモジュールを作成し、React NativeアプリケーションのJavaScriptからそのネイティブメソッドを呼び出しました。ネイティブモジュールメソッドで使用できる引数の型や、callbacks と promisesの設定方法などについて詳しく学ぶことができます。

## Beyond a Calendar Native Module

### Better Native Module Export

上記のように `NativeModules`からネイティブモジュールを引き出してインポートするのは少し不格好です。

ネイティブモジュールの利用者がネイティブモジュールにアクセスするたびにそのようなことをしなくて済むように、モジュールのJavaScriptラッパーを作成できます。次の内容の `CalendarModule.js` という名前の新しいJavaScriptファイルを作成してください。

```tsx
/**
* This exposes the native CalendarModule module as a JS module. This has a
* function 'createCalendarEvent' which takes the following parameters:

* 1. String name: A string representing the name of the event
* 2. String location: A string representing the location of the event
*/
import {NativeModules} from 'react-native';
const {CalendarModule} = NativeModules;
export default CalendarModule;
```

このJavaScriptファイルは、JavaScriptの副機能を追加するのにも良い場所になります。たとえば、TypeScriptのような型システムを使用している場合は、ここにネイティブモジュールの型注釈を追加できます。React Native はネイティブからJSへの型安全をまだサポートしていませんが、この型注釈の追加によりJSコードはすべて型安全になります。そうすることで、将来的に型安全なネイティブモジュールに簡単に切り替えることができます。以下は、CalendarModuleに型安全を追加する例です：

```tsx
/**
 * This exposes the native CalendarModule module as a JS module. This has a
 * function 'createCalendarEvent' which takes the following parameters:
 *
 * 1. String name: A string representing the name of the event
 * 2. String location: A string representing the location of the event
 */
import {NativeModules} from 'react-native';
const {CalendarModule} = NativeModules;
interface CalendarInterface {
  createCalendarEvent(name: string, location: string): void;
}
export default CalendarModule as CalendarInterface;
```

他のJavaScriptファイルでは、ネイティブモジュールにアクセスして、次のようにそのメソッドを呼び出すことができます。

```tsx
import CalendarModule from './CalendarModule';
CalendarModule.createCalendarEvent('foo', 'bar');
```

> これは、`CalendarModule`をインポートするファイルが`CalendarModule.js`と同じ階層にあることを前提としています。必要に応じて相対的インポートを更新してください。

### Argument Types

JavaScriptでネイティブモジュールメソッドが呼び出されると、React Native はJSオブジェクトの引数をJava/Kotlinオブジェクトの類似オブジェクトに変換します。したがって、たとえば、Javaネイティブモジュールのメソッドがdoubleを受け入れる場合、JSではnumberを使用してメソッドを呼び出す必要があります。React Native があなたのために変換を処理します。以下は、ネイティブモジュールメソッドでサポートされている引数の型と、それらがマップされるJavaScriptの同等の型のリストです。

| Java          | Kotlin        | JavaScript |
| ------------- | ------------- | ---------- |
| Boolean       | Boolean       | ?boolean   |
| boolean       |               | boolean    |
| Double        | Double        | ?number    |
| double        |               | number     |
| String        | String        | string     |
| Callback      | Callback      | Function   |
| Promise       | Promise       | Promise    |
| ReadableMap   | ReadableMap   | Object     |
| ReadableArray | ReadableArray | Array      |

> 今の時点では次の型はサポートされていますが、TurboModulesではサポートされません。そのため使用は避けた方が良いでしょう。：
>
> - Integer Java/Kotlin -> ?number
> - Float Java/Kotlin -> ?number
> - int Java -> number
> - float Java -> number

上に記載されていない引数の型については、自分で変換を処理する必要があります。たとえば、Androidでは、「Date」変換はすぐにはサポートされていません。`Date`型への変換は、次のようにネイティブメソッド内で自分で処理できます。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
    String dateFormat = "yyyy-MM-dd";
    SimpleDateFormat sdf = new SimpleDateFormat(dateFormat);
    Calendar eStartDate = Calendar.getInstance();
    try {
        eStartDate.setTime(sdf.parse(startDate));
    }

```

</TabItem>
<TabItem value="kotlin">

```kotlin
    val dateFormat = "yyyy-MM-dd"
    val sdf = SimpleDateFormat(dateFormat, Locale.US)
    val eStartDate = Calendar.getInstance()
    try {
        sdf.parse(startDate)?.let {
            eStartDate.time = it
        }
    }
```

</TabItem>
</Tabs>

### Exporting Constants

ネイティブモジュールは、JSで使用できるネイティブメソッド `getConstants()`を実装することで定数をエクスポートできます。以下では、「getConstants()」を実装して、JavaScriptでアクセスできる「DEFAULT_EVENT_NAME」定数を含むマップを返却します。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
@Override
public Map<String, Object> getConstants() {
   final Map<String, Object> constants = new HashMap<>();
   constants.put("DEFAULT_EVENT_NAME", "New Event");
   return constants;
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
override fun getConstants(): MutableMap<String, Any> =
    hashMapOf("DEFAULT_EVENT_NAME" to "New Event")
```

</TabItem>
</Tabs>

その後、JSのネイティブモジュールで `getConstants`を呼び出すことで定数にアクセスできます。

```tsx
const {DEFAULT_EVENT_NAME} = CalendarModule.getConstants();
console.log(DEFAULT_EVENT_NAME);
```

技術的には、`getConstants()`でエクスポートされた定数にネイティブモジュールオブジェクトから直接アクセスすることは可能です。これはTurboModulesではサポートされなくなるので、今後必要な移行を避けるために、コミュニティに上記のアプローチに切り替えることをお勧めしています。

> 現在の定数は初期化時にのみエクスポートされるため、実行時にgetConstantsの値を変更しても、JavaScript環境には影響しません。この仕様はTurbomodulesでは変更されます。Turbomodulesでは、`getConstants()`が通常のネイティブモジュールメソッドになり、各呼び出しがネイティブ側にヒットします。

### Callbacks

ネイティブモジュールは、callbackというユニークな種類の引数もサポートしています。callbackは、非同期メソッドでJava/KotlinからJavaScriptにデータを渡すために使用されます。また、ネイティブ側からJavaScriptを非同期で実行するためにも使用できます。

callback付きのネイティブモジュールメソッドを作成するには、まず「Callback」インターフェイスをインポートし、次にネイティブモジュールメソッドに新しい「Callback」型のパラメータを追加します。callback引数にはいくつかの微妙な違いがありますが、TurboModulesですぐに廃止される予定です。まず、関数の引数に含めることができるcallbackは、successCallbackとfailureCallbackの2つだけです。さらに、ネイティブモジュールのメソッド呼び出しの最後の引数は（関数の場合）successCallbackとして扱われ、ネイティブモジュールメソッド呼び出しの最後から2番目の引数は（関数の場合）失敗callbackとして扱われます。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
import com.facebook.react.bridge.Callback;

@ReactMethod
public void createCalendarEvent(String name, String location, Callback callBack) {
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
import com.facebook.react.bridge.Callback

@ReactMethod fun createCalendarEvent(name: String, location: String, callback: Callback) {}
```

</TabItem>
</Tabs>

Java/Kotlinメソッドでcallbackを呼び出して、JavaScriptに渡したいデータを指定できます。ネイティブコードからJavaScriptには、シリアル化可能なデータしか渡せない点に注意してください。ネイティブオブジェクトを渡す必要がある場合は `WriteableMaps`、コレクションを使用する必要がある場合は `WritableArrays`を使用してください。また、callbackはネイティブ関数が完了した直後には呼び出されないということも非常に重要です。以下の記述では、以前の呼び出しで作成されたイベントのIDがcallbackに渡されます。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
  @ReactMethod
   public void createCalendarEvent(String name, String location, Callback callBack) {
       Integer eventId = ...
       callBack.invoke(eventId);
   }
```

</TabItem>
<TabItem value="kotlin">

```kotlin
  @ReactMethod
  fun createCalendarEvent(name: String, location: String, callback: Callback) {
      val eventId = ...
      callback.invoke(eventId)
  }
```

</TabItem>
</Tabs>

このメソッドには、次のようにJavaScriptでアクセスできます。

```tsx
const onPress = () => {
  CalendarModule.createCalendarEvent(
    'Party',
    'My House',
    eventId => {
      console.log(`Created a new event with id ${eventId}`);
    },
  );
};
```

もう1つ注目すべき重要な点は、ネイティブモジュールメソッドは1つのコールバックしか呼び出すことができないということです。つまり、successCallbackまたはfailureCallbackのいずれかを呼び出すことができますが、両方を呼び出すことはできません。また、各コールバックは最大で一度にしか呼び出すことができません。ただし、ネイティブモジュールはコールバックを保存して後で呼び出すことができます。

コールバックによるエラー処理には2つの方法があります。1つ目は、Nodeの規則に従い、コールバックに渡された最初の引数をエラーオブジェクトとして扱うことです。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
  @ReactMethod
   public void createCalendarEvent(String name, String location, Callback callBack) {
       Integer eventId = ...
       callBack.invoke(null, eventId);
   }
```

</TabItem>
<TabItem value="kotlin">

```kotlin
  @ReactMethod
  fun createCalendarEvent(name: String, location: String, callback: Callback) {
      val eventId = ...
      callback.invoke(null, eventId)
  }
```

</TabItem>
</Tabs>

JavaScriptでは、最初の引数を調べることで、エラーが渡されたかどうかを確認できます。

```tsx
const onPress = () => {
  CalendarModule.createCalendarEvent(
    'testName',
    'testLocation',
    (error, eventId) => {
      if (error) {
        console.error(`Error found! ${error}`);
      }
      console.log(`event id ${eventId} returned`);
    },
  );
};
```

別のオプションは、onSuccessとonFailureのコールバックを使用することです。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
@ReactMethod
public void createCalendarEvent(String name, String location, Callback myFailureCallback, Callback mySuccessCallback) {
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
@ReactMethod
  fun createCalendarEvent(
      name: String,
      location: String,
      myFailureCallback: Callback,
      mySuccessCallback: Callback
  ) {}
```

</TabItem>
</Tabs>

次に、JavaScriptでは、エラー応答と成功応答に個別のコールバックを追加できます。

```tsx
const onPress = () => {
  CalendarModule.createCalendarEvent(
    'testName',
    'testLocation',
    error => {
      console.error(`Error found! ${error}`);
    },
    eventId => {
      console.log(`event id ${eventId} returned`);
    },
  );
};
```

### Promises

ネイティブモジュールは [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) を実行することもできます。これにより、特にES2016の [async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) 構文を使用する場合に、JavaScriptを簡略化できます。ネイティブモジュールのJava/Kotlinメソッドの最後のパラメータがPromiseの場合、対応するJSメソッドはJS Promiseオブジェクトを返します。

コールバックの代わりにプロミスを使用するように上記のコードをリファクタリングすると、次のようになります。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
import com.facebook.react.bridge.Promise;

@ReactMethod
public void createCalendarEvent(String name, String location, Promise promise) {
    try {
        Integer eventId = ...
        promise.resolve(eventId);
    } catch(Exception e) {
        promise.reject("Create Event Error", e);
    }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
@ReactMethod
fun createCalendarEvent(name: String, location: String, promise: Promise) {
    try {
        val eventId = ...
        promise.resolve(eventId)
    } catch (e: Throwable) {
        promise.reject("Create Event Error", e)
    }
}
```

</TabItem>
</Tabs>

> コールバックと同様に、ネイティブモジュールメソッドはPromiseをrejectまたはresolveすることができ（両方はできませんが）、最大で一度しか実行できません。つまり、成功コールバックまたは失敗コールバックのいずれかを呼び出すことができますが、両方を呼び出すことはできません。また、各コールバックは最大で一度にしか呼び出すことができません。ただし、ネイティブモジュールはコールバックを保存して後で呼び出すことができます。

このメソッドに対応するJavaScriptメソッドはPromiseを返します。つまり、async 関数内で `await` キーワードを使って関数を呼び出し、その結果を待つことができます。

```tsx
const onSubmit = async () => {
  try {
    const eventId = await CalendarModule.createCalendarEvent(
      'Party',
      'My House',
    );
    console.log(`Created a new event with id ${eventId}`);
  } catch (e) {
    console.error(e);
  }
};
```

rejectメソッドは、次の引数のさまざまな組み合わせを取ります。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
String code, String message, WritableMap userInfo, Throwable throwable
```

</TabItem>
<TabItem value="kotlin">

```kotlin
code: String, message: String, userInfo: WritableMap, throwable: Throwable
```

</TabItem>
</Tabs>

詳細については、`Promise.java`インターフェイスは [こちら](https://github.com/facebook/react-native/blob/main/packages/react-native/ReactAndroid/src/main/java/com/facebook/react/bridge/Promise.java)を参照してください。「ユーザー情報」が指定されていない場合、ReactNativeはそれをnullに設定します。残りのパラメータでは、React Native はデフォルト値を使用します。`message`引数は、エラーコールスタックの先頭に表示されるエラー `message`を提供します。以下は、Java/Kotlinでのreject呼び出しによってJavaScriptで表示されるエラーメッセージの例です。

Java/Kotlin reject call:

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
promise.reject("Create Event error", "Error parsing date", e);
```

</TabItem>
<TabItem value="kotlin">

```kotlin
promise.reject("Create Event error", "Error parsing date", e)
```

</TabItem>
</Tabs>

プロミスが拒否されたときのReact Native アプリのエラーメッセージ：

<figure>
  <img src="/docs/assets/native-modules-android-errorscreen.png" width="200" alt="Image of error message in React Native app." />
  <figcaption>Image of error message</figcaption>
</figure>

### Sending Events to JavaScript

ネイティブモジュールは、直接呼び出されなくてもイベントをJavaScriptに通知できます。たとえば、ネイティブのAndroidカレンダーアプリからのカレンダーイベントが間もなく発生するというリマインダーをJavaScriptに通知したい場合があります。これを行う最も簡単な方法は、以下のコードスニペットのように「ReactContext」から取得できる「RCTDeviceEventEmitter」を使用することです。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
...
import com.facebook.react.modules.core.DeviceEventManagerModule;
import com.facebook.react.bridge.WritableMap;
import com.facebook.react.bridge.Arguments;
...
private void sendEvent(ReactContext reactContext,
                      String eventName,
                      @Nullable WritableMap params) {
 reactContext
     .getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter.class)
     .emit(eventName, params);
}

private int listenerCount = 0;

@ReactMethod
public void addListener(String eventName) {
  if (listenerCount == 0) {
    // Set up any upstream listeners or background tasks as necessary
  }

  listenerCount += 1;
}

@ReactMethod
public void removeListeners(Integer count) {
  listenerCount -= count;
  if (listenerCount == 0) {
    // Remove upstream listeners, stop unnecessary background tasks
  }
}
...
WritableMap params = Arguments.createMap();
params.putString("eventProperty", "someValue");
...
sendEvent(reactContext, "EventReminder", params);
```

</TabItem>
<TabItem value="kotlin">

```kotlin
...
import com.facebook.react.bridge.WritableMap
import com.facebook.react.bridge.Arguments
import com.facebook.react.modules.core.DeviceEventManagerModule
...

private fun sendEvent(reactContext: ReactContext, eventName: String, params: WritableMap?) {
    reactContext
      .getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter::class.java)
      .emit(eventName, params)
}

private var listenerCount = 0

@ReactMethod
fun addListener(eventName: String) {
  if (listenerCount == 0) {
    // Set up any upstream listeners or background tasks as necessary
  }

  listenerCount += 1
}

@ReactMethod
fun removeListeners(count: Int) {
  listenerCount -= count
  if (listenerCount == 0) {
    // Remove upstream listeners, stop unnecessary background tasks
  }
}
...
val params = Arguments.createMap().apply {
    putString("eventProperty", "someValue")
}
...
sendEvent(reactContext, "EventReminder", params)
```

</TabItem>
</Tabs>

その後、JavaScriptモジュールは [NativeEventEmitter](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/EventEmitter/NativeEventEmitter.js) クラスの「addListener」メソッドによってイベントを受信するように登録できます。

```tsx
import {NativeEventEmitter, NativeModules} from 'react-native';
...
useEffect(() => {
    const eventEmitter = new NativeEventEmitter(NativeModules.ToastExample);
    let eventListener = eventEmitter.addListener('EventReminder', event => {
      console.log(event.eventProperty) // "someValue"
    });

    // Removes the listener once unmounted
    return () => {
      eventListener.remove();
    };
  }, []);
```

### Getting Activity Result from startActivityForResult

You'll need to listen to `onActivityResult` if you want to get results from an activity you started with `startActivityForResult`. To do this, you must extend `BaseActivityEventListener` or implement `ActivityEventListener`. The former is preferred as it is more resilient to API changes. Then, you need to register the listener in the module's constructor like so:

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
reactContext.addActivityEventListener(mActivityResultListener);
```

</TabItem>
<TabItem value="kotlin">

```kotlin
reactContext.addActivityEventListener(mActivityResultListener);
```

</TabItem>
</Tabs>

Now you can listen to `onActivityResult` by implementing the following method:

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
@Override
public void onActivityResult(
 final Activity activity,
 final int requestCode,
 final int resultCode,
 final Intent intent) {
 // Your logic here
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
override fun onActivityResult(
    activity: Activity?,
    requestCode: Int,
    resultCode: Int,
    intent: Intent?
) {
    // Your logic here
}
```

</TabItem>
</Tabs>

Let's implement a basic image picker to demonstrate this. The image picker will expose the method `pickImage` to JavaScript, which will return the path of the image when called.

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```kotlin
public class ImagePickerModule extends ReactContextBaseJavaModule {

  private static final int IMAGE_PICKER_REQUEST = 1;
  private static final String E_ACTIVITY_DOES_NOT_EXIST = "E_ACTIVITY_DOES_NOT_EXIST";
  private static final String E_PICKER_CANCELLED = "E_PICKER_CANCELLED";
  private static final String E_FAILED_TO_SHOW_PICKER = "E_FAILED_TO_SHOW_PICKER";
  private static final String E_NO_IMAGE_DATA_FOUND = "E_NO_IMAGE_DATA_FOUND";

  private Promise mPickerPromise;

  private final ActivityEventListener mActivityEventListener = new BaseActivityEventListener() {

    @Override
    public void onActivityResult(Activity activity, int requestCode, int resultCode, Intent intent) {
      if (requestCode == IMAGE_PICKER_REQUEST) {
        if (mPickerPromise != null) {
          if (resultCode == Activity.RESULT_CANCELED) {
            mPickerPromise.reject(E_PICKER_CANCELLED, "Image picker was cancelled");
          } else if (resultCode == Activity.RESULT_OK) {
            Uri uri = intent.getData();

            if (uri == null) {
              mPickerPromise.reject(E_NO_IMAGE_DATA_FOUND, "No image data found");
            } else {
              mPickerPromise.resolve(uri.toString());
            }
          }

          mPickerPromise = null;
        }
      }
    }
  };

  ImagePickerModule(ReactApplicationContext reactContext) {
    super(reactContext);

    // Add the listener for `onActivityResult`
    reactContext.addActivityEventListener(mActivityEventListener);
  }

  @Override
  public String getName() {
    return "ImagePickerModule";
  }

  @ReactMethod
  public void pickImage(final Promise promise) {
    Activity currentActivity = getCurrentActivity();

    if (currentActivity == null) {
      promise.reject(E_ACTIVITY_DOES_NOT_EXIST, "Activity doesn't exist");
      return;
    }

    // Store the promise to resolve/reject when picker returns data
    mPickerPromise = promise;

    try {
      final Intent galleryIntent = new Intent(Intent.ACTION_PICK);

      galleryIntent.setType("image/*");

      final Intent chooserIntent = Intent.createChooser(galleryIntent, "Pick an image");

      currentActivity.startActivityForResult(chooserIntent, IMAGE_PICKER_REQUEST);
    } catch (Exception e) {
      mPickerPromise.reject(E_FAILED_TO_SHOW_PICKER, e);
      mPickerPromise = null;
    }
  }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
class ImagePickerModule(reactContext: ReactApplicationContext) :
    ReactContextBaseJavaModule(reactContext) {

    private var pickerPromise: Promise? = null

    private val activityEventListener =
        object : BaseActivityEventListener() {
            override fun onActivityResult(
                activity: Activity?,
                requestCode: Int,
                resultCode: Int,
                intent: Intent?
            ) {
                if (requestCode == IMAGE_PICKER_REQUEST) {
                    pickerPromise?.let { promise ->
                        when (resultCode) {
                            Activity.RESULT_CANCELED ->
                                promise.reject(E_PICKER_CANCELLED, "Image picker was cancelled")
                            Activity.RESULT_OK -> {
                                val uri = intent?.data

                                uri?.let { promise.resolve(uri.toString())}
                                    ?: promise.reject(E_NO_IMAGE_DATA_FOUND, "No image data found")
                            }
                        }

                        pickerPromise = null
                    }
                }
            }
        }

    init {
        reactContext.addActivityEventListener(activityEventListener)
    }

    override fun getName() = "ImagePickerModule"

    @ReactMethod
    fun pickImage(promise: Promise) {
        val activity = currentActivity

        if (activity == null) {
            promise.reject(E_ACTIVITY_DOES_NOT_EXIST, "Activity doesn't exist")
            return
        }

        pickerPromise = promise

        try {
            val galleryIntent = Intent(Intent.ACTION_PICK).apply { type = "image\/*" }

            val chooserIntent = Intent.createChooser(galleryIntent, "Pick an image")

            activity.startActivityForResult(chooserIntent, IMAGE_PICKER_REQUEST)
        } catch (t: Throwable) {
            pickerPromise?.reject(E_FAILED_TO_SHOW_PICKER, t)
            pickerPromise = null
        }
    }

    companion object {
        const val IMAGE_PICKER_REQUEST = 1
        const val E_ACTIVITY_DOES_NOT_EXIST = "E_ACTIVITY_DOES_NOT_EXIST"
        const val E_PICKER_CANCELLED = "E_PICKER_CANCELLED"
        const val E_FAILED_TO_SHOW_PICKER = "E_FAILED_TO_SHOW_PICKER"
        const val E_NO_IMAGE_DATA_FOUND = "E_NO_IMAGE_DATA_FOUND"
    }
}
```

</TabItem>
</Tabs>

### Listening to Lifecycle Events

Listening to the activity's LifeCycle events such as `onResume`, `onPause` etc. is very similar to how `ActivityEventListener` was implemented. The module must implement `LifecycleEventListener`. Then, you need to register a listener in the module's constructor like so:

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
reactContext.addLifecycleEventListener(this);
```

</TabItem>
<TabItem value="kotlin">

```kotlin
reactContext.addLifecycleEventListener(this)
```

</TabItem>
</Tabs>

Now you can listen to the activity's LifeCycle events by implementing the following methods:

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
@Override
public void onHostResume() {
   // Activity `onResume`
}
@Override
public void onHostPause() {
   // Activity `onPause`
}
@Override
public void onHostDestroy() {
   // Activity `onDestroy`
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
override fun onHostResume() {
    // Activity `onResume`
}

override fun onHostPause() {
    // Activity `onPause`
}

override fun onHostDestroy() {
    // Activity `onDestroy`
}
```

</TabItem>
</Tabs>

### Threading

現在、Androidでは、すべてのネイティブモジュールの非同期メソッドは1つのスレッド上で実行されます。現在の割り当ては将来変更される可能性があるため、ネイティブモジュールはどのスレッドで呼び出されているかを推測しないでください。ブロッキングコールが必要な場合は、重い作業を内部で管理されているワーカースレッドにディスパッチし、そこからのcallbackを分散する必要があります。
