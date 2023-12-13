---
id: architecture-overview
title: Architecture Overview
slug: /overview
---

:::info
アーキテクチャの概要へようこそ！React Native を使い始める場合は、<a href="/docs/getting-started">Guides</a>セクションを参照してください。React Native が内部でどのように機能しているかを学ぶのであれば、このまま読み続けてください！

このセクションは作成中であり、今後さらに資料が追加される予定です。後で必ず戻って新しい情報を確認してください。
:::

アーキテクチャの概要は、React Nativeの内部がどのように機能するかについての概念的な概要を共有することを目的としています。対象読者には、ライブラリの著者と主要なコントリビュータが含まれます。アプリ開発者であれば、React Nativeを効果的に使うためにこの資料に精通している必要はありません。React Native が内部でどのように機能するかについての洞察が得られるので、概要からも恩恵を受けることができます。<a href="https://github.com/reactwg/react-native-new-architecture/discussions/9">このセクションのワーキンググループ内の議論について</a>、フィードバックをお気軽に共有してください。

## Table of Contents

- Rendering
  - [Fabric](fabric-renderer)
  - [Render, Commit, and Mount](render-pipeline)
  - [Cross Platform Implementation](xplat-implementation)
  - [View Flattening](view-flattening)
  - [Threading Model](threading-model)
- Build Tools
  - [Bundled Hermes](bundled-hermes)
- [Glossary](glossary)
