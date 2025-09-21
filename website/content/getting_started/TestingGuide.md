---
title: "テストガイド"
date: 2019-11-29T15:26:15Z
draft: false
weight: 40
---

{{< toc >}}

## コマンド早見表

以下のコマンドの詳細はこの先で説明しています。
全てのコマンドは cmake のビルドディレクトリである `build/` 以下で、
[プロジェクトのビルド](/getting_started/) 後に実行されています。

### MLIR のテストを全て実行する

```sh
cmake --build . --target check-mlir
```

### インテグレーションテストを実行する (`-DMLIR_INCLUDE_INTEGRATION_TESTS=ON` が必須です)

```sh
cmake --build . --target check-mlir-integration
```

### C++ のユニットテストを実行する

```sh
bin/llvm-lit -v tools/mlir/test/Unit
```

### 特定のディレクトリの `lit` テストを実行する

```sh
bin/llvm-lit -v tools/mlir/test/Dialect/Arith
```

### 特定の `lit` テストファイルを実行する

```sh
bin/llvm-lit -v tools/mlir/test/Dialect/Polynomial/ops.mlir
```

## テストの種類

### `lit` + `FileCheck` テスト

[`FileCheck`](https://llvm.org/docs/CommandGuide/FileCheck.html) は、
「2 つのファイルを読んで (一方は標準入力から、もう片方はコマンドで指定されたものから)、
一方をもう片方の検証に用いる」ツールです。片方のファイルは、
もう片方のファイルで現れるであろう文字列やパターンを指定する `CHECK` タグを持ちます。
MLIR では [`lit`](https://llvm.org/docs/CommandGuide/lit.html)
を用いて `mlir-opt` のようなツールの実行を制御しており、そして `FileCheck`
を用いて変換パスの出力など IR の違う側面を検証しています。

`lit`/`FileCheck` のソースファイルは、`mlir` ソースツリー以下の `mlir/test/` にあります。
このディレクトリ内では、テストは `mlir/include/mlir/` 内と大体同じ構造になっており、
`Dialect/`、`Transforms/`、`Conversion/` などのサブディレクトリがあります。

#### 例

`FileCheck` テストの例は以下の通りです。

```mlir
// RUN: mlir-opt %s -cse | FileCheck %s

// CHECK-LABEL: func.func @simple_constant
func.func @simple_constant() -> (i32, i32) {
  // CHECK-NEXT: %[[RESULT:.*]] = arith.constant 1
  // CHECK-NEXT: return %[[RESULT]], %[[RESULT]]

  %0 = arith.constant 1 : i32
  %1 = arith.constant 1 : i32
  return %0, %1 : i32, i32
}
```

`RUN` で始まるコメントは `lit` のディレクディブで、実行させるコマンドを指定します。
`%s` が現在のファイルを示す、といったような特殊なプレースホルダがいくつかあります。
`CHECK` で始まるコメントは `FileCheck` のディレクディブで、
出力に現れるであろう文字列やパターンを検証するのに使います。

上のテストで検証しているのは、共通部分式除去 (`-cse`) を行った後、
定数が 1 つだけ IR に残っており、単一の SSA 値が 2 回返されているかどうかです。

#### ビルドシステムの詳細

上記のようなテストを 1 回のコマンド実行で全て走らせる主な方法としては、
`check-mlir` を使うものがあります。

```sh
cmake --build . --target check-mlir
```

`check-mlir` ターゲットを実行するのは大体以下を実行するのと同じです
(ビルドディレクトリ内、ビルド後)。

```shell
./bin/llvm-lit tools/mlir/test
```

[Lit のドキュメント](https://llvm.org/docs/CommandGuide/lit.html)
に全てのオプションの詳細があります。

テストツリーの一部だけ実行したい場合は、 `tools/mlir/test`
よりも詳細なパスを入れてください。例えば、

```shell
./bin/llvm-lit tools/mlir/test/Dialect/Arith

# ファイル単位でのテストは可能ですが、これらのファイルは実際には
# ビルドディレクトリには存在していないため、ファイル名を事前に
# 把握している必要があります。
./bin/llvm-lit tools/mlir/test/Dialect/Arith/ops.mlir
```

あるいは全ての C++ ユニットテストを走らせるには、

```shell
./bin/llvm-lit tools/mlir/test/Unit
```

C++ ユニットテストは単体のバイナリとしても実行でき、
これはリビルドテストのサイクルを回す際に便利です。

```shell
# C++ の MLIRIRTests を実行するために必要な最小限のライブラリをリビルドする
cmake --build . --target tools/mlir/unittests/IR/MLIRIRTests

# MLIRIRTest C++ ユニットテストを直接実行する
tools/mlir/unittests/IR/MLIRIRTests

# 一部の C++ ユニットテストではこれでも動作します
LIT_OPTS="--filter=MLIRIRTests -a" cmake --build . --target check-mlir

# MLIRIRTests 内のうち指定した一つのサブセットを実行する
tools/mlir/unittests/IR/MLIRIRTests --gtest_filter=OpPropertiesTest.Properties
```

lit にはテスト実行を制御するいくつかのオプションがあります。
以下はそのうち特に開発用途に役に立つものです。

*   [`--filter=REGEXP`](https://llvm.org/docs/CommandGuide/lit.html#cmdoption-lit-filter) :
    名前が REGEXP にマッチするテストだけ実行します。
    環境変数 `LIT_FILTER` でも同様に指定できます。
*   [`--filter-out=REGEXP`](https://llvm.org/docs/CommandGuide/lit.html#cmdoption-lit-filter-out) :
    名前が REGEXP にマッチするテストを排除します。
    環境変数 `LIT_FILTER_OUT` でも同様に指定できます。
*   [`-a`](https://llvm.org/docs/CommandGuide/lit.html#cmdoption-lit-a) :
    全情報を表示します (少数のテストを回す際に役に立ちます)。
*   [`--time-tests`](https://llvm.org/docs/CommandGuide/lit.html#cmdoption-lit-time-tests) :
    遅いテストや全テストのヒストグラムなどかかった時間の統計を表示します。

どの lit のオプションでも環境変数 `LIT_OPS` から設定できます。
これは特に `check-mlir` をビルドシステムターゲットにした際に役に立ちます。

例:

```
# "python" が名前に含まれるテストを実行し全ての情報を表示する
LIT_OPTS="--filter=python -a" cmake --build . --target check-mlir

# LIT_FILTER を用い array_attributes python テストのみ実行する
LIT_FILTER="python/ir/array_attributes" cmake --build . --target check-mlir

# 例とインテグレーションテストを除いて全てのテストを実行する (その 2 つは遅いため)。
LIT_FILTER_OUT="Examples|Integrations" cmake --build . --target check-mlir
```

注意点として、上のコマンドは汎用的な cmake コマンドを用いて `check-mlir` ターゲットを実行してますが、
通常の場合ジェネレータを直接使用することでより簡潔に記述できます
(`ninja` でコンフィグされている場合は、
`cmake --build . --target check-mlir` の代わりに単に `ninja check-mlir` 書けます)。
このドキュメントでは一貫性のため汎用的な `cmake` を用いていますが、
インタラクティブなワークフローでは一般的に簡潔な方が望ましい場合が多いです。

### 診断ログテスト

MLIR は **診断ログ** と呼ばれるリッチなソースコード位置追跡システムを持っており、
コードベースのどこからでもエラーや警告の出力に用いることができます。
診断ログテストは、与えられた入力プログラムから特定の診断メッセージが出力されるかを検証します。


MLIR provides rich source location tracking that can be used to emit errors,
warnings, etc. from anywhere throughout the codebase, which are jointly called
*diagnostics*. Diagnostic tests assert that specific diagnostic messages are
emitted for a given input program. These tests are useful in that they allow
checking specific invariants of the IR without transforming or changing
anything.

Some examples of tests in this category are:

-   Verifying invariants of operations
-   Checking the expected results of an analysis
-   Detecting malformed IR

Diagnostic verification tests are written utilizing the
[source manager verifier handler](../docs/Diagnostics#sourcemgr-diagnostic-verifier-handler),
which is enabled via the `verify-diagnostics` flag in `mlir-opt`.

An example .mlir test running under `mlir-opt` is shown below:

```mlir
// RUN: mlir-opt %s -split-input-file -verify-diagnostics

// Expect an error on the same line.
func.func @bad_branch() {
  cf.br ^missing  // expected-error {{reference to an undefined block}}
}

// -----

// Expect an error on an adjacent line.
func.func @foo(%a : f32) {
  // expected-error@+1 {{invalid predicate attribute specification: "foo"}}
  %result = arith.cmpf "foo", %a, %a : f32
  return
}
```

### インテグレーションテスト

Integration tests are `FileCheck` tests that verify functional correctness of
MLIR code by running it, usually by means of JIT compilation using
`mlir-cpu-runner` and runtime support libraries.

Integration tests don't run by default. To enable them, set the
`-DMLIR_INCLUDE_INTEGRATION_TESTS=ON` flag during `cmake` configuration as
described in [Getting Started](_index.md).

```sh
cmake -G Ninja ../llvm \
   ... \
   -DMLIR_INCLUDE_INTEGRATION_TESTS=ON \
   ...
```

Now the integration tests run as part of regular testing.

```sh
cmake --build . --target check-mlir
```

To run only the integration tests, run the `check-mlir-integration` target.

```sh
cmake --build . --target check-mlir-integration
```

Note that integration tests are relatively expensive to run (primarily due to
JIT compilation), and tend to be trickier to debug (with multiple compilation
steps _integrated_, it usually takes a bit of triaging to find the root cause
of a failure). We reserve e2e tests for cases that are hard to verify
otherwise, e.g. when composing and testing complex compilation pipelines. In
those cases, verifying run-time output tends to be easier then the checking
e.g. LLVM IR with FileCheck. Lowering optimized `linalg.matmul` (with tiling
and vectorization) is a good example. For less involved lowering pipelines or
when there's almost 1-1 mapping between an Op and it's LLVM IR counterpart
(e.g. `arith.cmpi` and LLVM IR `icmp` instruction),  regular unit tests are considered
enough.

The source files of the integration tests are organized within the `mlir` source
tree by dialect (for example, `test/Integration/Dialect/Vector`).

#### ハードウェアエミュレータ

The integration tests include some tests for targets that are not widely
available yet, such as specific AVX512 features (like `vp2intersect`) and the
Intel AMX instructions. These tests require an emulator to run correctly
(lacking real hardware, of course). To enable these specific tests, first
download and install the
[Intel Emulator](https://software.intel.com/content/www/us/en/develop/articles/intel-software-development-emulator.html).
Then, include the following additional configuration flags in the initial set up
(X86Vector and AMX can be individually enabled or disabled), where `<path to
emulator>` denotes the path to the installed emulator binary. `sh cmake -G Ninja
../llvm \ ... \ -DMLIR_INCLUDE_INTEGRATION_TESTS=ON \
-DMLIR_RUN_X86VECTOR_TESTS=ON \ -DMLIR_RUN_AMX_TESTS=ON \
-DINTEL_SDE_EXECUTABLE=<path to emulator> \ ...` After this one-time set up, the
tests run as shown earlier, but will now include the indicated emulated tests as
well.

### C++ ユニットテスト

Unit tests are written using the
[googletest](https://google.github.io/googletest/) framework and are located in
the `mlir/unittests/` directory.

## コントリビュータガイドライン

In general, all commits to the MLIR repository should include an accompanying
test of some form. Commits that include no functional changes, such as API
changes like symbol renaming, should be tagged with NFC (No Functional Changes).
This signals to the reviewer why the change doesn't/shouldn't include a test.

`lit` tests with `FileCheck` are the preferred method of testing in MLIR for
non-erroneous output verification.

Diagnostic tests are the preferred method of asserting error messages are output
correctly. Every user-facing error message (e.g., `op.emitError()`) should be
accompanied by a corresponding diagnostic test.

When you cannot use the above, such as for testing a non-user-facing API like a
data structure, then you may write C++ unit tests. This is preferred because the
C++ APIs are not stable and subject to frequent refactoring. Using `lit` and
`FileCheck` allows maintainers to improve the MLIR internals more easily.

### FileCheck のベストプラクティス

FileCheck is an extremely useful utility, it allows for easily matching various
parts of the output. This ease of use means that it becomes easy to write
brittle tests that are essentially `diff` tests. FileCheck tests should be as
self-contained as possible and focus on testing the minimal set of
functionalities needed. Let's see an example:

```mlir
// RUN: mlir-opt %s -cse | FileCheck %s

// CHECK-LABEL: func.func @simple_constant() -> (i32, i32)
func.func @simple_constant() -> (i32, i32) {
  // CHECK-NEXT: %result = arith.constant 1 : i32
  // CHECK-NEXT: return %result, %result : i32, i32
  // CHECK-NEXT: }

  %0 = arith.constant 1 : i32
  %1 = arith.constant 1 : i32
  return %0, %1 : i32, i32
}
```

The above example is another way to write the original example shown in the main
[`lit` and `FileCheck` tests](#lit-and-filecheck-tests) section. There are a few
problems with this test; below is a breakdown of the no-nos of this test to
specifically highlight best practices.

*   Tests should be self-contained.

This means that tests should not test lines or sections outside of what is
intended. In the above example, we see lines such as `CHECK-NEXT: }`. This line
in particular is testing pieces of the Parser/Printer of FuncOp, which is
outside of the realm of concern for the CSE pass. This line should be removed.

*   Tests should be minimal, and only check what is absolutely necessary.

This means that anything in the output that is not core to the functionality
that you are testing should *not* be present in a CHECK line. This is a separate
bullet just to highlight the importance of it, especially when checking against
IR output.

If we naively remove the unrelated `CHECK` lines in our source file, we may end
up with:

```mlir
// CHECK-LABEL: func.func @simple_constant
func.func @simple_constant() -> (i32, i32) {
  // CHECK-NEXT: %result = arith.constant 1 : i32
  // CHECK-NEXT: return %result, %result : i32, i32

  %0 = arith.constant 1 : i32
  %1 = arith.constant 1 : i32
  return %0, %1 : i32, i32
}
```

It may seem like this is a minimal test case, but it still checks several
aspects of the output that are unrelated to the CSE transformation. Namely the
result types of the `arith.constant` and `return` operations, as well the actual
SSA value names that are produced. FileCheck `CHECK` lines may contain
[regex statements](https://llvm.org/docs/CommandGuide/FileCheck.html#filecheck-regex-matching-syntax)
as well as named
[string substitution blocks](https://llvm.org/docs/CommandGuide/FileCheck.html#filecheck-string-substitution-blocks).
Utilizing the above, we end up with the example shown in the main
[FileCheck tests](#filecheck-tests) section.

```mlir
// CHECK-LABEL: func.func @simple_constant
func.func @simple_constant() -> (i32, i32) {
  /// Here we use a substitution variable as the output of the constant is
  /// useful for the test, but we omit as much as possible of everything else.
  // CHECK-NEXT: %[[RESULT:.*]] = arith.constant 1
  // CHECK-NEXT: return %[[RESULT]], %[[RESULT]]

  %0 = arith.constant 1 : i32
  %1 = arith.constant 1 : i32
  return %0, %1 : i32, i32
}
```

### テストのフォーマットのベストプラクティス

When adding new tests, strive to follow these two key rules:

1. **Follow the existing naming and whitespace style.**
   - This applies when modifying existing test files that follow a particular
     convention, as it likely fits the context.
2. **Consistently document the edge case being tested.**
   - Clearly state what makes this test unique and how it complements other
     similar tests.

While the first rule extends LLVM’s general coding style to tests, the second
may feel new. The goal is to improve:

- **Test discoverability** – Well-documented tests make it easier to pair tests
  with patterns and understand their purpose.
- **Test consistency** – Consistent documentation and naming lowers cognitive
  load and helps avoid duplication.

A well-thought-out naming convention helps achieve all of the above.

---

#### 例：テストの可読性と名づけの改善

Consider these **three tests** that exercise `vector.maskedload -> vector.load`
lowering under the `-test-vector-to-vector-lowering` flag:

##### 改善前：一貫しておらず区別もしにくい

```mlir
// CHECK-LABEL:   func @maskedload_regression_1(
//  CHECK-SAME:       %[[A0:.*]]: memref<?xf32>,
//  CHECK-SAME:       %[[A1:.*]]: vector<16xf32>) -> vector<16xf32> {
//       CHECK:   %[[C0:.*]] = arith.constant 0 : index
//       CHECK:   %[[LOAD:.*]] = vector.load %[[A0]][%[[C]]]
//  CHECK-SAME:     : memref<?xf32>, vector<16xf32>
//       CHECK:   return %[[LOAD]] : vector<16xf32>
func.func @maskedload_regression_1(%arg0: memref<?xf32>, %arg1: vector<16xf32>) -> vector<16xf32> {
  %c0 = arith.constant 0 : index

  %vec_i1 = vector.constant_mask [16] : vector<16xi1>
  %ld = vector.maskedload %arg0[%c0], %vec_i1, %arg1
    : memref<?xf32>, vector<16xi1>, vector<16xf32> into vector<16xf32>

  return %ld : vector<16xf32>
}

// CHECK-LABEL:   func @maskedload_regression_2(
//  CHECK-SAME:       %[[A0:.*]]: memref<16xi8>,
//  CHECK-SAME:       %[[A1:.*]]: vector<16xi8>) -> vector<16xi8> {
//       CHECK:   %[[C0:.*]] = arith.constant 0 : index
//       CHECK:   %[[LOAD:.*]] = vector.load %[[A0]][%[[C]]]
//  CHECK-SAME:     : memref<16xi8>, vector<16xi8>
//       CHECK:   return %[[LOAD]] : vector<16xi8>
func.func @maskedload_regression_2(%arg0: memref<16xi8>, %arg1: vector<16xi8>) -> vector<16xi8> {
  %c0 = arith.constant 0 : index

  %vec_i1 = vector.constant_mask [16] : vector<16xi1>
  %ld = vector.maskedload %arg0[%c0], %vec_i1, %arg1
    : memref<16xi8>, vector<16xi1>, vector<16xi8> into vector<16xi8>

  return %ld : vector<16xi8>
}

// CHECK-LABEL:   func @maskedload_regression_3(
// CHECK-SAME:        %[[A0:.*]]: memref<16xf32>,
// CHECK-SAME:        %[[A1:.*]]: vector<16xf32>) -> vector<16xf32> {
//      CHECK:    return %[[A1]] : vector<16xf32>
func.func @maskedload_regression_3(%arg0: memref<16xf32>, %arg1: vector<16xf32>) -> vector<16xf32> {
  %c0 = arith.constant 0 : index

  %vec_i1 = vector.constant_mask [0] : vector<16xi1>
  %ld = vector.maskedload %arg0[%c0], %vec_i1, %arg1
    : memref<16xf32>, vector<16xi1>, vector<16xf32> into vector<16xf32>

  return %ld : vector<16xf32>
}
```

While all examples test `vector.maskedload` -> `vector.load lowering`, it is
difficult to tell their actual differences.

##### 改善ステップ 1 (一貫した変数名の導入)

To reduce cognitive load, use consistent names across MLIR and FileCheck (e.g.,
`%arg0` and `A0` above are not consistent). Also, instead of using generic
names like `%arg0` or `%vec_i1`, encode some additional context by using names
from existing documentation. For example from the Op documentation,
[`vector.maskedload`](https://mlir.lemon.ski/docs/Dialects/Vector/#vectormaskedload-vectormaskedloadop),
in this case, you can use `%base`, `%mask` and `%pass_thru`.

```mlir
// CHECK-LABEL:   func @maskedload_regression_1(
//  CHECK-SAME:       %[[BASE:.*]]: memref<?xf32>,
//  CHECK-SAME:       %[[PASS_THRU:.*]]: vector<16xf32>) -> vector<16xf32> {
// (...)
func.func @maskedload_regression_1(%base: memref<?xf32>, %pass_thru: vector<16xf32>) -> vector<16xf32> {
  // (...)
  %mask = vector.constant_mask [16] : vector<16xi1>
  %ld = vector.maskedload %base[%c0], %mask, %pass_thru (...)
  // (...)
}

// CHECK-LABEL:   func @maskedload_regression_2(
//  CHECK-SAME:       %[[BASE:.*]]: memref<16xi8>,
//  CHECK-SAME:       %[[PASS_THRU:.*]]: vector<16xi8>) -> vector<16xi8> {
// (...)
func.func @maskedload_regression_2(%base: memref<16xi8>, %pass_thru: vector<16xi8>) -> vector<16xi8> {
  // (...)
  %mask = vector.constant_mask [16] : vector<16xi1>
  %ld = vector.maskedload %base[%c0], %mask, %pass_thru (...)
  // (...)
}

// CHECK-LABEL:   func @maskedload_regression_3(
//  CHECK-SAME:       %[[BASE:.*]]: memref<16xf32>,
//  CHECK-SAME:       %[[PASS_THRU:.*]]: vector<16xf32>) -> vector<16xf32> {
// (...)
func.func @maskedload_regression_3(%base: memref<16xf32>, %pass_thru: vector<16xf32>) -> vector<16xf32> {
  // (...)
  %mask = vector.constant_mask [0] : vector<16xi1>
  %ld = vector.maskedload %base[%c0], %mask, %base (...)
  // (...)
}
```

##### 改善ステップ 2 (テスト名の改善)

Instead of using "regression" (which does not add unique information), rename
tests based on key attributes:

* All examples test the `vector.maskedload` to `vector.load` lowering.
* The first test uses a _dynamically_ shaped `memref`, while the others use
  _static_ shapes.
* The mask in the first two examples is "all true" (`vector.constant_mask
  [16]`), while it is "all false" (`vector.constant_mask [0]`) in the third
  example.
* The first and the third tests use `i32` elements, whereas the second uses
  `i8`.

This suggests the following naming scheme:
* `@maskedload_to_load_{static|dynamic}_{i32|i8}_{all_true|all_false}`.

```mlir
// CHECK-LABEL:   func @maskedload_to_load_dynamic_i32_all_true(
// (...)
func.func @maskedload_to_load_dynamic_i32_all_true(%base: memref<?xf32>, %pass_thru: vector<16xf32>) -> vector<16xf32> {
  // (...)
}

// CHECK-LABEL:   func @maskedload_to_load_static_i8_all_true(
// (...)
func.func @maskedload_to_load_static_i8_all_true(%base: memref<16xi8>, %pass_thru: vector<16xi8>) -> vector<16xi8> {
  // (...)
}

// CHECK-LABEL:   func @maskedload_to_load_static_i32_all_false(
// (...)
func.func @maskedload_to_load_static_i32_all_false(%base: memref<16xf32>, %pass_thru: vector<16xf32>) -> vector<16xf32> {
  // (...)
}
```

##### 改善ステップ 3 (新たに特定した不足しているケースを追加)

Step 2 made it possible to see that there is a case which is not tested:

* A mask that is neither "all true" nor "all false".

Unlike the existing cases, this mask must be preserved. In this scenario,
`vector.load` is not the right abstraction. Thus, no lowering should occur:

```mlir
// CHECK-LABEL:   func @negative_maskedload_to_load_static_i32_mixed(
// CHECK-SAME:        %[[BASE:.*]]: memref<16xf32>,
// CHECK-SAME:        %[[PASS_THRU:.*]]: vector<16xf32>) -> vector<16xf32> {
//      CHECK:    vector.maskedload
func.func @negative_maskedload_to_load_static_i32_mixed(%base: memref<16xf32>, %pass_thru: vector<16xf32>) -> vector<16xf32> {
  %c0 = arith.constant 0 : index
  %mask = vector.constant_mask [4] : vector<16xi1>

  %ld = vector.maskedload %base[%c0], %mask, %pass_thru
    : memref<16xf32>, vector<16xi1>, vector<16xf32> into vector<16xf32>

  return %ld : vector<16xf32>
}
```

The `negative_` prefix indicates that this test should fail to lower, as the
pattern should not match.

##### テストの命名規則
To summarize, here is the naming convention used in the examples above:

* `@{negative_}?maskedload_to_load_{static|dynamic}_{i32|i8}_{all_true|all_false|mixed}`.

The exact format may vary depending on context. However:
* **Avoid using suffixes** (e.g., `_fail`) to indicate negative tests — prefixes like
  `negative_` are easier to spot and grep for.
* Whatever naming convention you choose, **apply it consistently** throughout
  the test suite.

**Note:** In some cases, a prefix other than `negative_` might be more
appropriate. For instance, in "folding" tests where a pattern is expected not
to apply, using `no_` can be a more concise and equally clear alternative —
e.g., `@no_fold_<case>_<subcase>.`

#### 従うべき前例が見つからなかった場合

If you are adding a new test file, you can use other test files in the same
directory as inspiration.

If the test file you are modifying lacks a clear style and instead has mixed,
inconsistent styles, try to identify the dominant one and follow it. Even
better, consider refactoring the file to adopt a single, consistent style —
this helps improve our overall testing quality. Refactoring is also encouraged
when the existing style could be improved.

In many cases, it is best to create a separate PR for test refactoring to
reduce per-PR noise. However, this depends on the scale of changes — reducing
PR traffic is also important. Work with reviewers to use your judgment and
decide the best approach.

Alternatively, if you defer refactoring, consider creating a GitHub issue and
adding a TODO in the test file linking to it.

When creating a new naming convention, keep these points in mind:

* **Write Orthogonal Tests**
If naming is difficult then the tests may be lacking a clear purpose. A good
rule of thumb is to avoid testing the same thing repeatedly. Before writing
tests, define clear categories to cover (e.g., number of loops, data types).
This often leads to a natural naming scheme—for example: `@loop_depth_2_i32`.

* **What vs Why**
Test names should reflect _what_ is being tested, not _why_.

Encoding _why_ in test names can lead to overly long and complex names.
Instead, add inline comments where needed.

#### 常識を忘れないこと

Always apply common sense when naming functions and variables. Encoding too
much information in names makes the tests less readable and less maintainable.

Trust your judgment. When in doubt, consult your "future self": _"Will this still
make sense to me six months from now?_"

#### 最後のポイント - 主要原則

The above approach is just an example. It may not fit your use case perfectly,
so feel free to adapt it as needed.  Key principles to follow:

* Make tests self-documenting.
* Follow existing conventions.

These principles make tests easier to discover and maintain. For you, "future
you", and the rest of the MLIR community.

### テストのドキュメントのベストプラクティス

In addition to following good naming and formatting conventions, please
document your tests with comments. Focus on explaining **why** since the
**what** is usually clear from the code itself.

As an example, consider this test that uses the
`TransferWritePermutationLowering` pattern:


```mlir
/// Even with out-of-bounds accesses, it is safe to apply this pattern as it
/// does not modify which memory location is being accessed.

// CHECK-LABEL:   func.func @xfer_write_minor_identity_transposed_out_of_bounds
//  CHECK-SAME:      %[[VEC:.*]]: vector<4x8xi16>
//  CHECK-SAME:      %[[MEM:.*]]: memref<2x2x?x?xi16>
//  CHECK-SAME:      %[[IDX:.*]]: index)
//       CHECK:      %[[TR:.*]] = vector.transpose %[[VEC]], [1, 0]
//  CHECK_SAME:        : vector<4x8xi16> to vector<8x4xi16>

/// Expect the in_bounds attribute to be preserved. However, since we don't
/// print it when all flags are "false", it should not appear in the output.
/// CHECK-NOT:       in_bounds

// CHECK:           vector.transfer_write

/// The permutation map was replaced with vector.transpose
// CHECK-NOT:       permutation_map

// CHECK-SAME:        %[[TR]], %[[MEM]][%[[IDX]], %[[IDX]], %[[IDX]], %[[IDX]]]
// CHECK-SAME:        : vector<8x4xi16>, memref<2x2x?x?xi16>
func.func @xfer_write_minor_identity_transposed_out_of_bounds(
    %vec: vector<4x8xi16>,
    %mem: memref<2x2x?x?xi16>,
    %idx: index) {

  vector.transfer_write %vec, %mem[%idx, %idx, %idx, %idx] {
    in_bounds = [false, false],
    permutation_map = affine_map<(d0, d1, d2, d3) -> (d3, d2)>
  } : vector<4x8xi16>, memref<2x2x?x?xi16>

  return
}
```

The comments in the example above document two non-obvious behaviors:

* _Why_ is the `permutation_map` attribute missing from the output?
* _Why_ is the `in_bounds` attribute missing from the output?


#### どこに対しドキュメントが要るか判別するには
Think of yourself six months from now and ask: _"What might be difficult to
understand without comments?"_

If you expect something to be tricky for "future-you", it’s likely to be tricky
for others encountering the test for the first time.

#### テストを自己言及的にする
We can improve documentation further by:
* clarifying what pattern is being tested,
* providing high-level reasoning, and
* consolidating shared comments.

For example:

```mlir
///--------------------------------------------------------------------------------
/// [Pattern: TransferWritePermutationLowering]
///
/// IN: vector.transfer_write (_transposed_ minor identity permutation map)
/// OUT: vector.transpose + vector.transfer_write (minor identity permutation map)
///
/// Note: `permutation_map` from the input Op is replaced with the newly
/// inserted vector.traspose Op.
///--------------------------------------------------------------------------------
// CHECK-LABEL:   func.func @xfer_write_minor_identity_transposed
//       (...)
//       CHECK:      %[[TR:.*]] = vector.transpose (...)
//       CHECK:      vector.transfer_write %[[TR]] (...)
//       (...)
```

The example above documents:
* The transformation pattern being tested.
* The key logic behind the transformation.
* The expected change in output.


#### 「何」をドキュメントする
You should always document why, but documenting what is also valid and
encouraged in cases where:

* The test output is long and complex.
* The tested logic is non-trivial and/or involves multiple transformations.

For example, in this test for Linalg convolution vectorization, comments are
used to document high-level steps (original FileCheck "check" lines have been
trimmed for brevity):

```mlir
func.func @conv1d_nwc_4x2x8_memref(%input: memref<4x6x3xf32>, %filter: memref<1x3x8xf32>, %output: memref<4x2x8xf32>) {
  linalg.conv_1d_nwc_wcf
    {dilations = dense<1> : tensor<1xi64>, strides = dense<3> : tensor<1xi64>}
    ins(%input, %filter : memref<4x6x3xf32>, memref<1x3x8xf32>)
    outs(%output : memref<4x2x8xf32>)
  return
}

//      CHECK: func @conv1d_nwc_4x2x8_memref
// CHECK-SAME: (%[[INPUT:.+]]: memref<4x6x3xf32>, %[[FILTER:.+]]: memref<1x3x8xf32>, %[[OUTPUT:.+]]: memref<4x2x8xf32>)

/// Read the whole data in one shot.
//  CHECK-DAG:   %[[V_INPUT_R:.+]] = vector.transfer_read %[[INPUT]][%[[C0]], %[[C0]], %[[C0]]], %[[F0]]
//  CHECK-DAG:  %[[V_FILTER_R:.+]] = vector.transfer_read %[[FILTER]][%[[C0]], %[[C0]], %[[C0]]], %[[F0]]
//  CHECK-DAG:  %[[V_OUTPUT_R:.+]] = vector.transfer_read %[[OUTPUT]][%[[C0]], %[[C0]], %[[C0]]], %[[F0]]

//      CHECK:   %[[V_INPUT_0:.+]] = vector.extract_strided_slice %[[V_INPUT_R]]
//      CHECK:   %[[V_INPUT_1:.+]] = vector.extract_strided_slice %[[V_INPUT_R]]

//      CHECK:    %[[V_FILTER:.+]] = vector.extract %[[V_FILTER_R]][0] : vector<3x8xf32> from vector<1x3x8xf32>

//      CHECK:  %[[V_OUTPUT_0:.+]] = vector.extract_strided_slice %[[V_OUTPUT_R]]
//      CHECK:  %[[V_OUTPUT_1:.+]] = vector.extract_strided_slice %[[V_OUTPUT_R]]

/// w == 0, kw == 0
//      CHECK:   %[[CONTRACT_0:.+]] = vector.contract
// CHECK-SAME:     %[[V_INPUT_0]], %[[V_FILTER]], %[[V_OUTPUT_0]]

/// w == 1, kw == 0
//      CHECK:   %[[CONTRACT_1:.+]] = vector.contract
// CHECK-SAME:     %[[V_INPUT_1]], %[[V_FILTER]], %[[V_OUTPUT_1]]

/// w == 0, kw == 0
//      CHECK:   %[[RES_0:.+]] = vector.insert_strided_slice %[[CONTRACT_0]], %[[V_OUTPUT_R]]
/// w == 1, kw == 0
//      CHECK:   %[[RES_1:.+]] = vector.insert_strided_slice %[[CONTRACT_1]], %[[RES_0]]

/// Write the result back in one shot.
//      CHECK:   vector.transfer_write %[[RES_1]], %[[OUTPUT]][%[[C0]], %[[C0]], %[[C0]]]
```

Though the comments document _what_ is happening (e.g., "Write the result back
in one shot"), some variables — like `w` and `kw` — are not explained. This is
intentional - their purpose becomes clear when studying the corresponding
Linalg vectorizer implementation (or, when analysing how
`linalg.conv_1d_nwc_wcf` works).

Comments help you understand code, they do not replace the need to read it.
Comments guide the reader, they do not repeat what the code already says.

#### 最後のポイント - 主要原則
Below are key principles to follow when documenting tests:
* Always document _why_, document _what_ if you need to (e.g. the underlying
	logic is non-trivial).
* Use block comments for higher-level comments (e.g. to describe the patterns
	being tested).
* Think about maintainability - comments should help future developers (which
	includes you) understand tests at a glance.
* Avoid over-explaining. Comments should assist, not replace reading the code.
