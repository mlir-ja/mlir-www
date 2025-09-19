---
title: "非推奨・リファクタ中の機能"
date: 2023-02-16T15:26:15Z
draft: false
weight: 2
---

This page collects current deprecations in MLIR of API and features we intend
to remove soon, as well as large refactoring an migration on-going.
We try to list these here for the purpose of helping downstream users keeping
up with MLIR development.

## 非推奨の機能

### `matchAndRewrite` を `match` / `rewrite` の代わりに使用してください

The `match` and `rewrite` functions of `RewritePattern` and `ConversionPattern`
are deprecated. Use the combined `matchAndRewrite` instead.


### `vector.broadcast` を `vector.splat` の代わりに使用してください

The `vector.splat` operation has been deprecated.

[Discussion on Discourse](https://discourse.llvm.org/t/rfc-mlir-vector-deprecate-then-remove-vector-splat/87143)

### `OpTy::create(builder, loc, ...)` を `builder.create<OpTy>(loc, ...)` の代わりに使用してください

The `OpBuilder::create` API has been deprecated in favor of generated `OpTy::create` methods.

[Discussion on Discourse](https://discourse.llvm.org/t/psa-opty-create-now-with-100-more-tab-complete/87339)

## 実行中のリファクタリング・大きな変更

# 過去の非推奨・リファクタ

## LLVM 17

### 「約束されたインターフェース」と `FuncDialect` 使用時に InlinerExtension を明示的に登録する必要性

We're tightening the contract around injecting interfaces into the system externally, starting with
`DialectInterface`. As an important visible change at the moment is that if you're using the inliner
with the `FuncDialect`, you need to call `func::registerAllExtensions(registry);` when setting up
your `MLIRContext`.

### プロパティとそれに伴うジェネリックプリント形式の変更

See on [Discourse](https://discourse.llvm.org/t/rfc-introducing-mlir-operation-properties/67846/19).

Properties is a new feature in MLIR that will allow to separate the storage of
inherent attributes from the discardable ones. One key visible change is the
generic assembly format which gain a new attribute entry in-between `<` `>`.

### `preloadDialectInContext` は1年以上の非推奨期間の後削除されました

See https://github.com/llvm/llvm-project/commit/9c8db444bc85

If you have an mlir-opt tool and you’re still depending on
`preloadDialectInContext`, you need to revisit your pipeline. This option
used with mlir-opt is hiding issues with the pipeline, and indications of
missing getDependentDialects().
[Discussion on Discourse](https://discourse.llvm.org/t/psa-preloaddialectincontext-has-been-deprecated-for-1y-and-will-be-removed/68992)

### `mlir-opt` 様ツールが `MlirOptMainConfig` を使うようマイグレーション

See https://github.com/llvm/llvm-project/commit/ffd6f6b91a3

If your `mlir-opt`-like tool is using the
`MlirOptMain(int argc, char **argv, ...)` entry point you won’t be affected,
otherwise, see the
[Discussion on Discourse](https://discourse.llvm.org/t/psa-migrating-mlir-opt-like-tools-to-use-mliroptmainconfig/68991)

### GPU コンパイル設定属性の登場による `gpu-to-(cubin|hsaco)` の非推奨化

[GPU compilation attributes](https://mlir.lemon.ski/docs/Dialects/GPU/#gpu-compilation) are a completely new mechanism for handling the compilation
of GPU modules down to binary or other formats in an extensible way. This mechanism lifts
many current restrictions the GPU serialization passes had, like being present only if the
CUDA driver is there or not linking to LibDevice.

One key difference is the usage of `ptxas` or the `nvptxcompiler` library for compiling PTX
to binary; hence the CUDATollkit is required for generating binaries.

For these attributes to work correctly, making registration calls to `registerNVVMTargetInterfaceExternalModels`,
`registerROCDLTargetInterfaceExternalModels` and `registerOffloadingLLVMTranslationInterfaceExternalModels` are necessary.

The passes `gpu-to-(cubin|hsaco)` will be removed in a future release.

## LLVM 18

### LLVM 方言における不透明ポインタの使用について

LLVM 17 has stopped officially supporting typed pointers, and MLIRs LLVM Dialect
is now in the process of dropping the support as well. This was announced back
in February 2023 ([PSA](https://discourse.llvm.org/t/psa-in-tree-conversion-passes-can-now-be-used-with-llvm-opaque-pointers-please-switch-your-downstream-projects/68738))
, and now the final steps, i.e., removing the typed pointers, have started
([PSA](https://discourse.llvm.org/t/psa-removal-of-typed-pointers-from-the-llvm-dialect/74502)).
If you are still targeting LLVM dialect with typed pointers, an update to
support opaque pointers will be necessary.


## LLVM 19

### GPU コンパイル設定属性の登場による `gpu-to-(cubin|hsaco)` の削除

**Notice: The passes `gpu-to-(cubin|hsaco)` have been removed from the monorepo, use target attributes instead. See the LLVM 17 section on this page for more information.**

## LLVM 20

### `vector.reshape` の削除

This operation was added back in 2019, and since then, no lowerings or uses have
been implemented in upstream MLIR or any known downstream projects. Due to this
lack of use, it was decided that the operation should be removed.

[Discussion on Discourse](https://discourse.llvm.org/t/rfc-should-vector-reshape-be-removed/80478)

## LLVM 21

### `dyn_cast`/`cast`/`isa`/... はフリー関数版を使用してください

When casting attributes or type, use the free functions variants, e.g.,
`dyn_cast<T>(x)`, `isa<T>(x)`, etc. Use of the cast methods variants (e.g.,
`x.dyn_cast<T>()`) should be avoided in new code as we removed these
methods in https://github.com/llvm/llvm-project/pull/135556.

[Discussion on Discourse](https://discourse.llvm.org/t/preferred-casting-style-going-forward/68443)
