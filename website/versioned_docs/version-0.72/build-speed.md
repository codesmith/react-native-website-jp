---
id: build-speed
title: Speeding up your Build phase
---

React Native アプリのビルドは**高価**で、開発者の時間が数分かかる可能性があります。
これは、プロジェクトが大きくなるにつれて、また一般的には複数のReact Native 開発者がいる大規模な組織で問題になる可能性があります。

このパフォーマンスへの影響を軽減するために、このページでは**ビルド時間を改善**する方法についていくつかの提案を紹介します。

:::info
**Androidの新しいアーキテクチャ** でビルド時間が遅くなっていることに気付いた場合は、React Native 0.71にアップグレードすることをお勧めします
:::

## Build only one ABI during development (Android-only)

Android アプリをローカルでビルドする場合、デフォルトで 4 つの [Application Binary Interfaces (ABIs)](https://developer.android.com/ndk/guides/abis): `armeabi-v7a`, `arm64-v8a`, `x86`, `x86_64` をすべてビルドします。

ただし、ローカルでビルドしてエミュレータをテストしたり、物理デバイス上でテストしたりする場合は、すべてをビルドする必要はないでしょう。

これにより、**ネイティブビルド時間**が最大 75% 短縮されるはずです。

React Native CLI を使用している場合は、`run-android` コマンドに `--active-arch-only` フラグを追加できます。このフラグは、実行中のエミュレータまたは接続されている電話から正しい ABI が確実に取得されるようにします。この方法が正常に機能していることを確認するために、コンソールに `info Detected architectures arm64-v8a` のようなメッセージが表示されます。

```
$ yarn react-native run-android --active-arch-only

[ ... ]
info Running jetifier to migrate libraries to AndroidX. You can disable it using "--no-jetifier" flag.
Jetifier found 1037 file(s) to forward-jetify. Using 32 workers...
info JS server already running.
info Detected architectures arm64-v8a
info Installing the app...
```

このメカニズムは `reactNativeArchitectures` Gradle プロパティに依存しています。

そのため、コマンドラインから Gradle を使用して CLI なしで直接ビルドする場合は、ビルドしたい ABI を次のように指定できます。

```
$ ./gradlew :app:assembleDebug -PreactNativeArchitectures=x86,x86_64
```

これは、CI で Android アプリを構築し、マトリックスを使用してさまざまなアーキテクチャのビルドを並列化する場合に便利です。

ご希望であれば、プロジェクトの [top-level folder](https://github.com/facebook/react-native/blob/19cf70266eb8ca151aa0cc46ac4c09cb987b2ceb/template/android/gradle.properties#L30-L33) にある `gradle.properties` ファイルを使用して、この値をローカルでオーバーライドすることもできます。

```
# Use this property to specify which architecture you want to build.
# You can also override it from the CLI using
# ./gradlew <task> -PreactNativeArchitectures=x86_64
reactNativeArchitectures=armeabi-v7a,arm64-v8a,x86,x86_64
```

アプリの**リリースバージョン**をビルドしたら、それらのフラグを削除することを忘れないでください。日常の開発ワークフローで使用しているABIだけでなく、すべてのABIで機能するapk/appバンドルをビルドするためです。

## Use a compiler cache

ネイティブビルド (C++ または Objective-C) を頻繁に実行する場合は、**コンパイラキャッシュ**を使用すると便利な場合があります。

具体的には、ローカルコンパイラキャッシュと分散コンパイラキャッシュの 2 種類のキャッシュを使用できます。

### Local caches

:::info
以下の説明は**AndroidとiOS**の両方で動作します。
Android アプリのみを構築しているなら、準備は万端です。
iOS アプリもビルドする場合は、以下の [XCode Specific Setup](#xcode-specific-setup) セクションの指示に従ってください。
:::

[**ccache**](https://ccache.dev/) を使用してネイティブビルドのコンパイルをキャッシュすることをお勧めします。
ccacheは、C++コンパイラをラップしてコンパイル結果を保存し、中間コンパイル結果が元々保存されていた場合はコンパイルをスキップすることで機能します。

インストールするには、[official installation instructions](https://github.com/ccache/ccache/blob/master/doc/INSTALL.md) に従ってください。

macOS では、`brew install ccache` を使って ccache をインストールできます。
インストールしたら、NDK のコンパイル結果をキャッシュするように次のように設定できます。

```
ln -s $(which ccache) /usr/local/bin/gcc
ln -s $(which ccache) /usr/local/bin/g++
ln -s $(which ccache) /usr/local/bin/cc
ln -s $(which ccache) /usr/local/bin/c++
ln -s $(which ccache) /usr/local/bin/clang
ln -s $(which ccache) /usr/local/bin/clang++
```

これにより、`/usr/local/bin/` 内に `ccache` へのシンボリックリンクが作成され、`gcc`、`g++` などと呼ばれます。

これは、デフォルトの `$PATH` 変数内で `/usr/local/bin/` が `/usr/bin/` より先にある限り有効です。

`which` コマンドを使用して動作することを確認できます。

```
$ which gcc
/usr/local/bin/gcc
```

結果が `/usr/local/bin/gcc` の場合、`ccache` を呼び出していることになり、`gcc` の呼び出しがラップされます。

:::caution
この `ccache` の設定は、React Native に関連するコンパイルだけでなく、マシンで実行しているすべてのコンパイルに影響することに注意してください。自己責任で使用してください。他のソフトウェアのインストール/コンパイルに失敗している場合は、これが原因である可能性があります。その場合は、次のようにして作成したシンボリックリンクを削除できます。

```
unlink /usr/local/bin/gcc
unlink /usr/local/bin/g++
unlink /usr/local/bin/cc
unlink /usr/local/bin/c++
unlink /usr/local/bin/clang
unlink /usr/local/bin/clang++
```

マシンを元の状態に戻し、デフォルトのコンパイラを使用します。
:::

その後、クリーンビルドを 2 回実行できます (例えば Android では、最初に `yarn react-native run-android` を実行し、`android/app/build` フォルダーを削除して、最初のコマンドをもう一度実行することができます)。2 番目のビルドは 1 番目のビルドよりもはるかに高速であることがわかります (数分ではなく数秒かかるはずです)。
ビルド中に `ccache` が正しく動作することを確認し、キャッシュのヒット/ミス率 `ccache -s` を確認できます

```
$ ccache -s
Summary:
  Hits:             196 /  3068 (6.39 %)
    Direct:           0 /  3068 (0.00 %)
    Preprocessed:   196 /  3068 (6.39 %)
  Misses:          2872
    Direct:        3068
    Preprocessed:  2872
  Uncacheable:        1
Primary storage:
  Hits:             196 /  6136 (3.19 %)
  Misses:          5940
  Cache size (GB): 0.60 / 20.00 (3.00 %)
```

`ccache` はすべてのビルドの統計情報を集計していることに注意してください。`ccache --zero-stats` を使用してビルドの前にリセットし、キャッシュヒット率を確認できます。

キャッシュを消去する必要がある場合は、`ccache --clear` を使って消去できます

#### XCode Specific Setup

`ccache` が iOS と XCode で正しく動作することを確認するには、さらにいくつかの手順を実行する必要があります。

1. Xcode と `xcodebuild` がコンパイラコマンドを呼び出す方法を変更する必要があります。デフォルトでは、コンパイラバイナリへの _完全に指定されたパス_ を使用するため、`/usr/local/bin` にインストールされているシンボリックリンクは使用されません。これら2つのオプションのいずれかを使用して、コンパイラに _相対_ 名を使用するようにXcodeを構成できます。

- 直接コマンドラインを使用する場合は、コマンドラインにプレフィックスが付く環境変数:`CLANG=clang CLANGPLUSPLUS=clang++ LD=clang LDPLUSPLUS=clang++ xcodebuild <rest of xcodebuild command line>`
- `pod install` ステップ中に Xcode ワークスペースのコンパイラを変更する`ios/Podfile` の `post_install` セクション

```ruby
  post_install do |installer|
    react_native_post_install(installer)

    # ...possibly other post_install items here

    installer.pods_project.targets.each do |target|
      target.build_configurations.each do |config|
        # Using the un-qualified names means you can swap in different implementations, for example ccache
        config.build_settings["CC"] = "clang"
        config.build_settings["LD"] = "clang"
        config.build_settings["CXX"] = "clang++"
        config.build_settings["LDPLUSPLUS"] = "clang++"
      end
    end

    __apply_Xcode_12_5_M1_post_install_workaround(installer)
  end
```

2. Xcode のコンパイル時に ccache がキャッシュヒットを記録するように、ある程度のずさんな動作やキャッシュ動作を許容する ccache 設定が必要です。標準と異なる ccache 設定変数は、環境変数で設定すると以下のようになります。

```bash
export CCACHE_SLOPPINESS=clang_index_store,file_stat_matches,include_file_ctime,include_file_mtime,ivfsoverlay,pch_defines,modules,system_headers,time_macros
export CCACHE_FILECLONE=true
export CCACHE_DEPEND=true
export CCACHE_INODECACHE=true
```

`ccache.conf` ファイルや ccache が提供する他のメカニズムでも同じように設定できます。これについての詳細は [official ccache manual](https://ccache.dev/manual/4.3.html) にあります。

#### Using this approach on a CI

Ccacheは、macOSの `/Users/$USER/Library/Caches/ccache` フォルダーを使用してキャッシュを保存します。
したがって、対応するフォルダーをCIにも保存および復元して、ビルドを高速化できます。

ただし、注意すべき点がいくつかあります。

1. CIでは、ポイズンドキャッシュの問題を回避するために、フルクリーンビルドを行うことをお勧めします。前の段落で説明したアプローチに従えば、4 つの異なる ABI でネイティブビルドを並列化できるはずで、CI では `ccache` は必要ないでしょう。

2. `ccache` はタイムスタンプを利用してキャッシュヒットを計算します。CIを実行するたびにファイルが再ダウンロードされるため、これはCIではうまく機能しません。これを解決するには、[hashing the content of the file](https://ccache.dev/manual/4.3.html) の代わりに `compiler_check content` オプションを使用する必要があります。

### Distributed caches

ローカルキャッシュと同様に、ネイティブビルドにも分散キャッシュを使用することを検討してください。
これは、ネイティブビルドを頻繁に行う大規模な組織で特に役立つ可能性があります。

これを実現するには [sccache](https://github.com/mozilla/sccache) を使用することをお勧めします。
このツールの設定方法と使用方法については、sccache [distributed compilation quickstart](https://github.com/mozilla/sccache/blob/main/docs/DistributedQuickstart.md) を参照してください。
