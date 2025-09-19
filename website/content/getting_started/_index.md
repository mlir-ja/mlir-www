---
title: "はじめに"
date: 2019-11-29T15:26:15Z
draft: false
weight: 10
---

MLIR のチュートリアルをお忘れなく！
[スライド](https://llvm.org/devmtg/2020-09/slides/MLIR_Tutorial.pdf) -
[動画](https://www.youtube.com/watch?v=Y4SvqTtOIDk) -
[オンラインチュートリアル](https://mlir.lemon.ski/docs/Tutorials/Toy/)


LLVM をビルドするには [LLVM Getting Started](https://llvm.org/docs/GettingStarted.html)
が参考になります。以下は LLVM で MLIR をビルドする方法の簡単な説明です。

以下の MLIR をコンパイル・テストする方法の説明では、
`git`、[`ninja`](https://ninja-build.org/)、そして動作する
C++ ツールチェーンが環境にインストールされていることを前提にしています
([LLVM requirements](https://llvm.org/docs/GettingStarted.html#requirements) を参照)。

手始めに、Toy 言語のコンパイラビルドを [チュートリアル](docs/Tutorials/Toy/Ch-1.md) で試せます。

---

**ヒント**

通常の開発で役に立つテストの起動・フィルタ方法についての説明は
[テストガイド](TestingGuide/#command-line-incantations)
を参照してください。

---

### Unix ライクな環境でのコンパイル・テスト

```sh
git clone https://github.com/llvm/llvm-project.git
mkdir llvm-project/build
cd llvm-project/build
cmake -G Ninja ../llvm \
   -DLLVM_ENABLE_PROJECTS=mlir \
   -DLLVM_BUILD_EXAMPLES=ON \
   -DLLVM_TARGETS_TO_BUILD="Native;NVPTX;AMDGPU" \
   -DCMAKE_BUILD_TYPE=Release \
   -DLLVM_ENABLE_ASSERTIONS=ON
# clang と lld を用いることでビルドを高速にするには、以下を追加してください
#  -DCMAKE_C_COMPILER=clang -DCMAKE_CXX_COMPILER=clang++ -DLLVM_ENABLE_LLD=ON
# CCache でリビルドをさらに劇的に速くするには、以下を追加してください
#  -DLLVM_CCACHE_BUILD=ON
# オプションとして、以下で ASAN/UBSAN が有効になり開発中のバグを早期に発見できます
# -DLLVM_USE_SANITIZER="Address;Undefined"
# オプションとして、以下でインテグレーションテストを有効にできます
# -DMLIR_INCLUDE_INTEGRATION_TESTS=ON
cmake --build . --target check-mlir
```

マシンへの `clang` 及び `lld` のインストールを行い (例えば Ubuntu
では `sudo apt-get install clang lld`) 、そして最後の cmake
コマンドにあるコメント部分を外すことが推奨されています。

デバッグ情報が欲しい場合、`-DCMAKE_BUILD_TYPE=Debug` もしくは
`-DCMAKE_BUILD_TYPE=RelWithDebInfo` を利用できます。
`-DLLVM_USE_SPLIT_DWARF=ON` を併用することが推奨されており、これによりデバッグビルド時に
30%～40% 程度のディスク容量が削減できます。

---

### Windows でのコンパイル・テスト
Windows で Visual Studio 2017 を用いてコンパイル及びテストをする場合、以下のようにしてください。

```bat
REM In shell with Visual Studio environment set up, e.g., with command such as
REM   $visual-studio-install\Auxiliary\Build\vcvarsall.bat" x64
REM invoked.
git clone https://github.com/llvm/llvm-project.git
mkdir llvm-project\build
cd llvm-project\build
cmake ..\llvm -G "Visual Studio 15 2017 Win64" -DLLVM_ENABLE_PROJECTS=mlir -DLLVM_BUILD_EXAMPLES=ON -DLLVM_TARGETS_TO_BUILD="Native" -DCMAKE_BUILD_TYPE=Release -Thost=x64 -DLLVM_ENABLE_ASSERTIONS=ON
cmake --build . --target tools/mlir/test/check-mlir
```
