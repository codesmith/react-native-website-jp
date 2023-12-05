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

Importing your native module by pulling it off of `NativeModules` like above is a bit clunky.

To save consumers of your native module from needing to do that each time they want to access your native module, you can create a JavaScript wrapper for the module. Create a new JavaScript file named `CalendarModule.js` with the following content:

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

This JavaScript file also becomes a good location for you to add any JavaScript side functionality. For example, if you use a type system like TypeScript you can add type annotations for your native module here. While React Native does not yet support Native to JS type safety, all your JS code will be type safe. Doing so will also make it easier for you to switch to type-safe native modules down the line. Below is an example of adding type safety to the CalendarModule:

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

In your other JavaScript files you can access the native module and invoke its method like this:

```tsx
import CalendarModule from './CalendarModule';
CalendarModule.createCalendarEvent('foo', 'bar');
```

> This assumes that the place you are importing `CalendarModule` is in the same hierarchy as `CalendarModule.js`. Please update the relative import as necessary.

### Argument Types

When a native module method is invoked in JavaScript, React Native converts the arguments from JS objects to their Java/Kotlin object analogues. So for example, if your Java Native Module method accepts a double, in JS you need to call the method with a number. React Native will handle the conversion for you. Below is a list of the argument types supported for native module methods and the JavaScript equivalents they map to.

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

> The following types are currently supported but will not be supported in TurboModules. Please avoid using them:
>
> - Integer Java/Kotlin -> ?number
> - Float Java/Kotlin -> ?number
> - int Java -> number
> - float Java -> number

For argument types not listed above, you will need to handle the conversion yourself. For example, in Android, `Date` conversion is not supported out of the box. You can handle the conversion to the `Date` type within the native method yourself like so:

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

A native module can export constants by implementing the native method `getConstants()`, which is available in JS. Below you will implement `getConstants()` and return a Map that contains a `DEFAULT_EVENT_NAME` constant you can access in JavaScript:

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

The constant can then be accessed by invoking `getConstants` on the native module in JS:

```tsx
const {DEFAULT_EVENT_NAME} = CalendarModule.getConstants();
console.log(DEFAULT_EVENT_NAME);
```

Technically it is possible to access constants exported in `getConstants()` directly off the native module object. This will no longer be supported with TurboModules, so we encourage the community to switch to the above approach to avoid necessary migration down the line.

> That currently constants are exported only at initialization time, so if you change getConstants values at runtime it won't affect the JavaScript environment. This will change with Turbomodules. With Turbomodules, `getConstants()` will become a regular native module method, and each invocation will hit the native side.

### Callbacks

Native modules also support a unique kind of argument: a callback. Callbacks are used to pass data from Java/Kotlin to JavaScript for asynchronous methods. They can also be used to asynchronously execute JavaScript from the native side.

In order to create a native module method with a callback, first import the `Callback` interface, and then add a new parameter to your native module method of type `Callback`. There are a couple of nuances with callback arguments that will soon be lifted with TurboModules. First off, you can only have two callbacks in your function arguments- a successCallback and a failureCallback. In addition, the last argument to a native module method call, if it's a function, is treated as the successCallback, and the second to last argument to a native module method call, if it's a function, is treated as the failure callback.

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

You can invoke the callback in your Java/Kotlin method, providing whatever data you want to pass to JavaScript. Please note that you can only pass serializable data from native code to JavaScript. If you need to pass back a native object you can use `WriteableMaps`, if you need to use a collection use `WritableArrays`. It is also important to highlight that the callback is not invoked immediately after the native function completes. Below the ID of an event created in an earlier call is passed to the callback.

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

This method could then be accessed in JavaScript using:

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

Another important detail to note is that a native module method can only invoke one callback, one time. This means that you can either call a success callback or a failure callback, but not both, and each callback can only be invoked at most one time. A native module can, however, store the callback and invoke it later.

There are two approaches to error handling with callbacks. The first is to follow Node’s convention and treat the first argument passed to the callback as an error object.

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

In JavaScript, you can then check the first argument to see if an error was passed through:

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

Another option is to use an onSuccess and onFailure callback:

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

Then in JavaScript you can add a separate callback for error and success responses:

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

Native modules can also fulfill a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise), which can simplify your JavaScript, especially when using ES2016's [async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) syntax. When the last parameter of a native module Java/Kotlin method is a Promise, its corresponding JS method will return a JS Promise object.

Refactoring the above code to use a promise instead of callbacks looks like this:

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

> Similar to callbacks, a native module method can either reject or resolve a promise (but not both) and can do so at most once. This means that you can either call a success callback or a failure callback, but not both, and each callback can only be invoked at most one time. A native module can, however, store the callback and invoke it later.

The JavaScript counterpart of this method returns a Promise. This means you can use the `await` keyword within an async function to call it and wait for its result:

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

The reject method takes different combinations of the following arguments:

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

For more detail, you can find the `Promise.java` interface [here](https://github.com/facebook/react-native/blob/main/packages/react-native/ReactAndroid/src/main/java/com/facebook/react/bridge/Promise.java). If `userInfo` is not provided, ReactNative will set it to null. For the rest of the parameters React Native will use a default value. The `message` argument provides the error `message` shown at the top of an error call stack. Below is an example of the error message shown in JavaScript from the following reject call in Java/Kotlin.

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

Error message in React Native App when promise is rejected:

<figure>
  <img src="/docs/assets/native-modules-android-errorscreen.png" width="200" alt="Image of error message in React Native app." />
  <figcaption>Image of error message</figcaption>
</figure>

### Sending Events to JavaScript

Native modules can signal events to JavaScript without being invoked directly. For example, you might want to signal to JavaScript a reminder that a calendar event from the native Android calendar app will occur soon. The easiest way to do this is to use the `RCTDeviceEventEmitter` which can be obtained from the `ReactContext` as in the code snippet below.

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

JavaScript modules can then register to receive events by `addListener` on the [NativeEventEmitter](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/EventEmitter/NativeEventEmitter.js) class.

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

To date, on Android, all native module async methods execute on one thread. Native modules should not have any assumptions about what thread they are being called on, as the current assignment is subject to change in the future. If a blocking call is required, the heavy work should be dispatched to an internally managed worker thread, and any callbacks distributed from there.
