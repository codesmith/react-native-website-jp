---
id: timers
title: Timers
---

タイマーはアプリケーションの重要な部分であり、React Native は [browser timers](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Timeouts_and_intervals) を実装しています。

## Timers

- setTimeout, clearTimeout
- setInterval, clearInterval
- setImmediate, clearImmediate
- requestAnimationFrame, cancelAnimationFrame

`requestAnimationFrame(fn)` は `setTimeout(fn, 0)` とは異なります。前者はすべてのフレームがフラッシュされた後に起動しますが、後者は可能な限り速く (iPhone 5Sでは毎秒1000倍以上) 起動します。

`setImmediate` は、現在の JavaScript 実行ブロックの最後、つまりバッチ処理された応答をネイティブに送り返す直前に実行されます。`setImmediate` コールバック内で `setImmediate` を呼び出すと、すぐに実行され、その間で native に戻ることはないことに注意してください。

`Promise` 実装は `setImmediate` を非同期実装として使用します。

:::note
Android でのデバッグ時に、デバッガーとデバイス間の時間がずれると、アニメーションやイベントの動作などが正しく機能しなかったり、結果が不正確になったりすることがあります。
デバッガマシンで `` adb shell "date `date +%m%d%H%M%Y.%S%3N`" `` を実行して、これを修正してください。実際のデバイスで使用するにはルートアクセスが必要です。
:::

## InteractionManager

よく構築されたネイティブアプリが非常にスムーズに感じられる理由の 1 つは、インタラクションやアニメーション中のコストのかかる操作を回避できることです。React Native では、現在、JSの実行スレッドは1つしかないという制限がありますが、`InteractionManager` を使用して、インタラクション/アニメーションが完了した後に長時間実行される作業を開始するようにスケジュールできます。

アプリケーションでは、以下とのやりとりの後に実行するタスクをスケジュールできます。

```tsx
InteractionManager.runAfterInteractions(() => {
  // ...long-running synchronous task...
});
```

これを他のスケジューリング方法と比較してください。

- requestAnimationFrame(): 時間の経過とともにビューをアニメーション化するコード用。
- setImmediate/setTimeout/setInterval(): 後でコードを実行します。これによりアニメーションが遅れる可能性があることに注意してください。
- runAfterInteractions(): アクティブなアニメーションを遅らせることなく、後でコードを実行します。

タッチハンドリングシステムは、1 つ以上のアクティブなタッチを「インタラクション」と見なし、すべてのタッチが終了するかキャンセルされるまで `runAfterInteractions()` コールバックを遅延させます。

InteractionManagerでは、アニメーションの開始時にインタラクションの「ハンドル」を作成し、完了時にクリアすることで、アプリケーションがアニメーションを登録することもできます。

```tsx
const handle = InteractionManager.createInteractionHandle();
// run animation... (`runAfterInteractions` tasks are queued)
// later, on animation completion:
InteractionManager.clearInteractionHandle(handle);
// queued tasks run if all handles were cleared
```
