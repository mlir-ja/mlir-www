---
title: "非推奨・リファクタ中の機能"
date: 2023-02-16T15:26:15Z
draft: false
weight: 2
---

このページでは、MLIR における近日削除予定の API および機能、
そして大きなリファクタ及びマイグレーションを掲載しています。
ここでリストアップすることにより、ダウンストリームのユーザーが MLIR
の開発状況についていけるようにしています。

## 非推奨の機能

### `matchAndRewrite` を `match` / `rewrite` の代わりに使用してください

`RewritePattern` と`ConversionPattern` の `match` 及び `rewrite` 関数は非推奨になりました。 
統合された `matchAndRewrite` を使用してください。


### `vector.broadcast` を `vector.splat` の代わりに使用してください

`vector.splat` op は非推奨になりました。

[Discourse 上での議論](https://discourse.llvm.org/t/rfc-mlir-vector-deprecate-then-remove-vector-splat/87143)

### `OpTy::create(builder, loc, ...)` を `builder.create<OpTy>(loc, ...)` の代わりに使用してください

`OpBuilder::create` API は、op ごとに生成される `OpTy::create` メソッドの登場により非推奨になりました。

[Discourse 上での議論](https://discourse.llvm.org/t/psa-opty-create-now-with-100-more-tab-complete/87339)

## 実行中のリファクタリング・大きな変更

# 過去の非推奨・リファクタ

## LLVM 17

### 「約束されたインターフェース」と `FuncDialect` 使用時に InlinerExtension を明示的に登録する必要性

インターフェースをシステム外部から注入する際の契約条件の強化を、 `DialectInterface` を皮切りに開始しています。
現在特に注目すべき変更点としては、 `FuncDialect` を用いたインライナーを使用している場合は、
`MLIRContext` のセットアップ時に `func::registerAllExtensions(registry);` を呼ぶ必要があります。

### プロパティとそれに伴う汎用プリント形式の変更

[Discourse](https://discourse.llvm.org/t/rfc-introducing-mlir-operation-properties/67846/19) をご覧ください。

プロパティは、op 固有の属性と消しても良い属性を区別できるようにするための
MLIR の新機能です。主な目に見える変更としては、汎用アセンブリ形式において
`<` と `>` で囲まれた新しい属性エントリが追加される点があります。

### `preloadDialectInContext` は1年以上の非推奨期間の後削除されました

https://github.com/llvm/llvm-project/commit/9c8db444bc85 をご覧ください。

もし使用されている mlir-opt ツールが `preloadDialectInContext` に依存している場合、
パイプラインを見直す必要があります。
このオプションを mlir-opt で利用した場合、パイプラインの問題が隠され、
getDependentDialects() 不足の警告が表示されない場合があります。
[Discourse 上での議論](https://discourse.llvm.org/t/psa-preloaddialectincontext-has-been-deprecated-for-1y-and-will-be-removed/68992)

### `mlir-opt` 様ツールが `MlirOptMainConfig` を使うようマイグレーション

https://github.com/llvm/llvm-project/commit/ffd6f6b91a3 をご覧ください。

お使いの `mlir-opt` 様のツールが
`MlirOptMain(int argc, char **argv, ...)` エントリポイントを使用している場合、影響はありません。
その他の場合、
[Discourse 上での議論](https://discourse.llvm.org/t/psa-migrating-mlir-opt-like-tools-to-use-mliroptmainconfig/68991)
をご覧ください。

### GPU コンパイル設定属性の登場による `gpu-to-(cubin|hsaco)` の非推奨化

[GPU コンパイル設定属性](https://mlir.lemon.ski/docs/Dialects/GPU/#gpu-compilation) は、
GPU モジュールをバイナリやその他形式にコンパイルする際の設定を拡張性のある形で制御するための全く新しいメカニズムです。
この仕組みにより、従来の GPU シリアライゼーションパスが抱えていた多くの制約が解消されます。
例えば、CUDA ドライバが存在する場合にのみ利用可能であったり、LibDevice にリンクしないといった制約です。

重要な変更点としては、PTX をバイナリにコンパイルする際に `pxtas` あるいは `nvptxcompiler` ライブラリを用いることで、
そのためバイナリの生成には CUDAToolkit が必要です。

この属性が正しく動作するためには、
`registerNVVMTargetInterfaceExternalModels`、`registerROCDLTargetInterfaceExternalModels`、
そして `registerOffloadingLLVMTranslationInterfaceExternalModels` といった登録関数の呼び出しが必要です。

`gpu-to-(cubin|hsaco)` パスは将来のリリースで削除されます。

## LLVM 18

### LLVM 方言における不透明ポインタの使用について

LLVM 17 で公式に型付きポインタのサポートが終了され、MLIR の LLVM 方言でもこのサポートを切っている最中です。
これは 2023 年の 2 月 ([PSA](https://discourse.llvm.org/t/psa-in-tree-conversion-passes-can-now-be-used-with-llvm-opaque-pointers-please-switch-your-downstream-projects/68738))
に告知され、現在最終ステップであるところの型付きポインタの削除が開始されました
([PSA](https://discourse.llvm.org/t/psa-removal-of-typed-pointers-from-the-llvm-dialect/74502))。
型付きポインタを用いた LLVM 方言をターゲットにしている場合、
不透明ポインタをサポートするようなアップデートが必要です。


## LLVM 19

### GPU コンパイル設定属性の登場による `gpu-to-(cubin|hsaco)` の削除

**お知らせ: `gpu-to-(cubin|hsaco)` パスはモノレポから削除されたため、ターゲット属性を代わりに使用してください。詳しくはこのページの LLVM 17 セクションをご覧ください。**

## LLVM 20

### `vector.reshape` の削除

この op は 2019 年に追加されて以来、MLIR や MLIR を利用したプロジェクトで変換元 op となったり、
あるいは使用されたことはありませんでした。
使用例が無いことにより、この op の削除が決定しました。

[Discourse 上での議論](https://discourse.llvm.org/t/rfc-should-vector-reshape-be-removed/80478)

## LLVM 21

### `dyn_cast`/`cast`/`isa`/... はフリー関数版を使用してください

属性や型をキャストする際には、例えば
`dyn_cast<T>(x)` や `isa<T>(x)` などのようなフリー関数版を使用してください。
`x.dyn_cast<T>()` のようなキャストメソッドは、
https://github.com/llvm/llvm-project/pull/135556 で削除されるため新しいコードでは使用しないでください。

[Discourse 上での議論](https://discourse.llvm.org/t/preferred-casting-style-going-forward/68443)
