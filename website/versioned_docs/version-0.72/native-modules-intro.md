---
id: native-modules-intro
title: Native Modules Intro
---

import NativeDeprecated from './the-new-architecture/\_markdown_native_deprecation.mdx'

<NativeDeprecated />

React Native アプリは、例えばApple PayやGoogle PayにアクセスするためのネイティブAPIなど、JavaScriptではデフォルトでは利用できないネイティブプラットフォームAPIにアクセスする必要がある場合があります。既存のObjective-C、Swift、Java、C++ライブラリをJavaScriptで再実装せずに再利用したり、画像処理などのために高性能のマルチスレッドコードを書いたりしたいかもしれません。

ネイティブモジュールシステムは、Java/Objective-C/C++ (ネイティブ) クラスのインスタンスをJSオブジェクトとしてJavaScript (JS) に公開します。これにより、JS内から任意のネイティブコードを実行することができます。この機能が一般的な開発プロセスの一部になるとは思っていませんが、存在自体は必要不可欠です。React Native がJSアプリに必要なネイティブAPIをエクスポートしていない場合は、自分でエクスポートすることができます！

## Native Module Setup

React Native アプリケーション用のネイティブモジュールを書く方法は2つあります。

1. React Native アプリケーションのiOS/Androidプロジェクト内で直接
2. あなたや他の人のReact Nativeアプリケーションに、依存関係としてインストールされるNPMパッケージとして

このガイドでは、まず、React Nativeアプリケーション内にネイティブモジュールを直接実装する方法について説明します。しかしながら、以降のガイドでビルドするネイティブモジュールは、NPMパッケージとして配布することもできます。興味があれば、[ネイティブモジュールをNPMパッケージとしてセットアップ](native-modules-setup) ガイドをチェックしてください。

## Getting Started

次のセクションでは、React Native Nativeアプリケーション内でネイティブモジュールを直接構築する方法のガイドを紹介します。前提条件として、その中で作業するにはReact Native アプリケーションが必要です。React Nativeアプリケーションをまだ持っていない場合は、[この](getting-started) ステップに従ってセットアップできます。

カレンダーイベントを作成するために、React Nativeアプリケーション内のJavaScriptからiOS/AndroidのネイティブカレンダーAPIにアクセスしたいと想像してみてください。React Nativeは、ネイティブカレンダーライブラリと通信するためのJavaScript APIを公開していません。ただし、ネイティブモジュールを通じて、ネイティブカレンダーAPIと通信するネイティブコードを書くことができます。そうすれば、React NativeアプリケーションのJavaScriptを使ってそのネイティブコードを呼び出すことができます。

次のセクションでは、[Android](native-modules-android) と [iOS](native-modules-ios)の両方で、このようなカレンダーネイティブモジュールを作成します。
