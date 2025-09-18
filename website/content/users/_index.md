---
title: "MLIR の利用事例"
date: 2023-05-04T21:00:54Z
draft: false
weight: 1
---

以下アルファベット順で紹介します。

## [Accera](https://github.com/microsoft/Accera)

Accera は、アセンブリコードを手書きすることなくループ最適化の実験を可能にするコンパイラです。
Accera を使用すれば、このような問題や制約を最適化された方法で解決できます。
Python ライブラリとして利用可能で、幅広いプロセッサターゲットへのクロスコンパイルをサポートしています。

## [Allo](https://github.com/cornell-zhang/allo)

Allo は、Python 組み込みのアクセラレータ設計言語 (ADL) 及びコンパイラです。
大規模かつ高性能なハードウェアアクセラレータをモジュラーかつ組み合わせ可能な方法で構築できます。
漸進的なハードウェアカスタマイズ、再利用可能でパラメータ化されたカーネルテンプレート、
そして MLIR を基盤とした組み合わせ可能なスケジュールをサポートしており、
効率的なハードウェア開発を実現します。

## [Beaver](https://github.com/beaver-lodge/beaver)

Beaver は Elixir 及び Zig の MLIR フロントエンドです。
Elixir の組み合わせ可能なモジュール性とメタプログラミング機能により、
MLIR に対しシンプルで直観的、そして拡張性の高いインターフェースを提供します。

## [Bᴛᴏʀ2ᴍʟɪʀ](https://github.com/jetafese/btor2mlir): ハードウェア検証のためのフォーマットとツールチェーン

Bᴛᴏʀ2ᴍʟɪʀ は、MLIR をハードウェア検証分野に応用することで、
フォーマットの特性を最大限に活用する簡潔な手法を提供します。
具体的には、Bᴛᴏʀ2 フォーマットで表現されたハードウェア検証問題に対して、
ソフトウェア検証手法を適用することを可能にしています。
本プロジェクトは形式検証分野における研究の促進と支援を目的としており、
既存の手法と比較しても競争力のある性能を発揮することが実証されています。

## [Catalyst](https://github.com/PennyLaneAI/catalyst)

Catalyst は、[PennyLane](https://pennylane.ai/) 向けの AOT / JIT コンパイラであり、
ハイブリッド量子プログラムの高速化を実現します。主な特徴は以下の通りです。

- カスタム量子勾配と [Enzyme](https://github.com/EnzymeAD/Enzyme)
  ベースの逆伝播法による完全な自動微分機能
- 動的な量子プログラミングモデル
- Python 機械学習エコシステムとの統合機能

Catalyst にはデフォルトで高性能シミュレータ [Lightning](https://github.com/PennyLaneAI/pennylane-lightning)
が搭載されていますが、GPU や QPU (量子処理ユニット) を含む異種アーキテクチャ上での実行を可能にする拡張可能なバックエンドシステムもサポートしており、常に進化を続けています。

## [CIRCT](https://github.com/llvm/circt): 回路 IR コンパイラとツール

CIRCT プロジェクトは、MLIR とLLVM の開発手法をハードウェア設計ツール分野に適用することを目的とした (実験的な) 取り組みです。

## [Concrete](https://github.com/zama-ai/concrete): Python プログラムを FHE 相当に変換する TFHE コンパイラ

Concrete はオープンソースのフレームワークで、[完全準同型暗号](https://fhe.org) (FHE) の利用を簡素化し、
開発者が FHE を活用したプログラムを容易に作成できるように設計されています。

FHE は、データを復号することなく暗号化された状態で計算処理を行うことを可能にする強力な技術です。
これにより、ユーザーのプライバシーが保護されるとともに、
データ漏洩に対する強固なセキュリティ対策が実現されます。

Concrete を使用することで、開発者は様々なユースケースに対応したプライバシー保護アプリケーションを効率的に開発できます。
例えば、[Concrete ML](https://github.com/zama-ai/concrete-ml) は Concrete を基盤として構築されており、
機械学習アプリケーションにおいて FHE のプライバシー保護機能をシームレスに統合することが可能です。

## [DSP-MLIR](https://github.com/MPSLab-ASU/DSP_MLIR): MLIR によるデジタル信号処理アプリケーション用フレームワーク

DSP-MLIR は、 DSP (デジタル信号処理) アプリケーション向けに特別に設計されたフレームワークです。
本フレームワークでは、DSL (フロントエンド) 、コンパイラ、および DSP
特有のパターンを検出して DSP の定理に基づいた最適化を適用する書き換えパターンが提供されます。
サポート対象の DSP 演算機能は多岐にわたり、フィルタ処理 (FIR フィルタ、IIR フィルタ、フィルタ応答)、
変換処理 (DCT、FFT、IFFT) をはじめ、遅延処理やゲイン調整などの信号処理演算、
さらにアプリケーション開発向けの追加機能を備えています。

## [Enzyme](https://enzyme.mit.edu): MLIR 用の自動微分ツール

Enzyme (特に EnzymeMLIR) は、MLIR 向けの第一級自動微分システムです。
演算子とデータ型はそれぞれ、微分可能な動作を規定する汎用インターフェースを実装または継承しており、
これにより Enzyme は効率的な順方向および逆方向パスにおける微分値の計算を実現します。
ソースコードは [こちら](https://github.com/EnzymeAD/Enzyme/tree/main/enzyme/Enzyme/MLIR) から入手可能です。
また、 [Enzyme-JaX](https://github.com/EnzymeAD/Enzyme-JAX) プロジェクトもご参照ください。
このプロジェクトでは Enzyme を使用して StableHLO の微分処理を行い、
その結果として JaX 向けの MLIR ネイティブな微分計算とコード生成を実現しています。

## [Firefly](https://github.com/GetFirefly/firefly): BEAM 言語用の新しいコンパイラとランタイム

Firefly は単なるコンパイラではなく、ランタイム環境としても機能します。
以下の 2 つの主要なコンポーネントで構成されています。

- 指定されたターゲット (x86、ARM、WebAssemblyなど) 向けに
  Erlang をネイティブコードにコンパイルするコンパイラ
- Rust で実装された、OTP を実装するために必要な基本機能を提供する Erlang ランタイム

Firefly の開発の主な動機は、WebAssembly をターゲットとして Elixir アプリケーションをコンパイル可能にし、
Elixir をフロントエンド開発言語として活用できるようにすることでした。
さらに、 x86 などのプラットフォーム向けに自己完結型の実行ファイルを生成することで、
他のプラットフォームへのターゲット設定も可能です。

## [Flang](https://github.com/llvm/llvm-project/tree/main/flang)

Flang は、現代的な C++ でゼロから実装された Fortran フロントエンドです。
元々は [f18 プロジェクト](https://github.com/flang-compiler/f18) として開始され、
従来の [Flangプロジェクト](https://github.com/flang-compiler/flang) を置き換え、
その様々な欠点を解消することを目的としていました。
その後、f18 は LLVM プロジェクトに正式に採用され、名称も Flang に変更されました。
Fortran コンパイラの高レベル IR は、MLIR を用いてモデル化されています。

## [HEIR](https://github.com/google/heir)

HEIR (Homomorphic Encryption Intermediate Representation) は、
Google が開発した MLIR ベースのツールチェーンで、準同型暗号を利用したプログラムのコンパイルを目的としています。
準同型暗号技術により、データを復号することなく暗号化された状態のまま直接計算処理を行うことが可能となり、
これにより計算処理の全過程においてデータのプライバシーが保護されます。

MLIR を基盤とする　HEIR は、準同型暗号を対象としたコンパイラ開発のための柔軟で拡張性の高いフレームワークを提供します。
このアプローチにより、コードの最適化や変換をモジュラーかつスケーラブルな方法で実現することが可能となります。

## [IREE](https://github.com/google/iree)

IREE (発音: イーリー) は、Vulkan に準拠したハードウェア抽象化レイヤー (HAL) 
上で実行可能な ML モデルをコンパイルするためのコンパイラおよび最小限のランタイムシステムです。
GPU (Vulkan/SPIR-V経由)、CPU、あるいはこれらを組み合わせた方式など、
多様な小型～中型システム上で ML デバイスをコンパイル・実行するための実用的なソリューションを提供することを目的としています。
さらに、既存の Vulkan API ユーザー、特にゲーム開発やレンダリングパイプラインの分野において、
シームレスな相互運用性を実現することも目指しています。

## [JSIR](https://github.com/google/jsir)

JSIR は次世代型 JavaScript 解析ツールです。その中核には MLIR ベースの高レベル中間表現が採用されており、
データフロー解析とソースへの損失なし変換の両方をサポートしています。
この独自の設計により、ソースコードからソースコードへの変換処理に適しています。
Google では JSIR を、悪意のある JavaScript ファイルの解析・検出や、
Ads、Android、Chrome などの製品保護のために活用しています。

## [Kokkos](https://kokkos.org)

Kokkos C++ パフォーマンスポータビリティエコシステムは、
ハードウェアに依存しない方法で現代的な C++ アプリケーションを開発するための本番環境対応ソリューションです。
これは、米国エネルギー省のエクサスケールプロジェクトの一環であり、
米国における次世代スーパーコンピューティングプラットフォームに向けた HPC コミュニティの準備を推進する主要な取り組みです。
このエコシステムは、アプリケーションの開発と保守をポータブルな方法で行うための主要な課題に対応する複数のライブラリで構成されています。
主要な構成要素として、Kokkos コアプログラミングモデル、Kokkos カーネル数学ライブラリ、
及び Kokkos プロファイリング/デバッグツールの3つが挙げられます。

現在、MLIR をポータブルな Kokkos ベースのソースコードに変換し、
MLIR にパーティション方言を追加することでタイリングおよび分散スパーステンソルをサポートし、
空間データフローアクセラレータをターゲットとする機能を実装する
[作業](https://github.com/kokkos/kokkos.github.io/files/13651039/Kokkos_MLIR.pdf) が進められています。

## [Lingo DB](https://www.lingo-db.com): コンパイラ技術によるデータ処理の革新

LingoDB は、コンパイラ技術を駆使した最先端のデータ処理システムです。
パフォーマンスを犠牲にすることなく、従来にない柔軟性と拡張性を実現しています。
宣言型サブオペレータのサポートにより、リレーショナル SQL
クエリをはるかに超えた多様なデータ処理ワークフローに対応可能です。
さらに、 LingoDB は異なるドメインの最適化パスを相互に織り交ぜることで領域横断的な最適化を実現し、
その柔軟性によって異種ハードウェア環境への持続可能な対応を可能にしています。

LingoDB は、クエリを効率的なマシンコードにコンパイルする際に遅延を最小限に抑えるため、
MLIR コンパイラフレームワークを主な基盤として構築されています。

## [MARCO](https://github.com/marco-compiler/marco): Modelica 発展研究用コンパイラ

MARCO は Modelica 言語向けのプロトタイプコンパイラであり、
大規模モデルの効率的なコンパイルとシミュレーションに重点を置いて開発されています。
Modelica ソースコードは外部ツールによって処理され、
Modelica 言語に依存しない Base Modelica 形式の表現に変換されます。
この変換のために、MLIR の方言が特別に設計されています。

本プロジェクトには、C++ で記述された複数のランタイムライブラリが付属しており、
これらのライブラリは生成されたシミュレーションの駆動、補助機能の提供、
及び外部の微分方程式ソルバーとの連携を容易にするために使用されます。

## [MLIR-AIE](https://github.com/Xilinx/mlir-aie): AMD/Xilinx AIEngine デバイス用ツールチェーン

MLIR-AIE は、Versal AIEngine ベースデバイス向けの低レベルデバイス構成を提供するツールチェーンです。
プロセッサ、ストリームスイッチ、TileDMA、ShimDMA ブロックなど、
デバイスの AIEngine 部分を対象としたサポートを提供します。
LibXAIE ライブラリをターゲットとしたバックエンドコード生成機能を備えており、
さらに高度な設計を可能にする高レベルな抽象化機能も実装されています。

## [MLIR-DaCe](https://github.com/spcl/mlir-dace): データ中心 MLIR 方言

MLIR-DaCe は、制御中心型とデータ中心型の中間表現間のギャップを埋めることを目的としたプロジェクトです。
この 2 種類の中間表現を橋渡しすることで、
制御中心型とデータ中心型の最適化手法を最適化パイプライン内で統合可能にします。
この目的を達成するため、MLIR-DaCe は MLIR と
DaCe フレームワークを接続するデータ中心型方言を MLIR に提供しています。

## [MLIR-EmitC](https://github.com/iml130/mlir-emitc)

MLIR-EmitC は、機械学習モデルを C++ コードに変換するための機能を提供します。
このリポジトリには、Keras および TensorFlow モデルを
[TOSA](https://mlir.lemon.ski/docs/Dialects/TOSA/) 及び
[StableHLO](https://github.com/openxla/stablehlo/) 方言に変換するためのスクリプトとツール、
およびそれらを [EmitC](https://mlir.lemon.ski/docs/Dialects/EmitC/) 形式に変換する機能が含まれています。
EmitC形式は、参照実装への呼び出しを生成するために使用されます。

EmitC 方言自体および C++ コード生成器は、MLIR コアの一部となっており、
現在は MLIR-EmitC リポジトリとしては提供されていません。

## [Mojo](https://docs.modular.com/mojo/)

Mojo は、Python の構文の優れた部分とシステムプログラミング、メタプログラミングを統合し、
MLIR エコシステムを活用することで、研究と実運用の間のギャップを埋める新しいプログラミング言語です。
Python の厳密な上位互換となること (既存の Python プログラムと互換性があること) を目指し、
特に長期サポートが必要なエコシステムの実現に向けて CPython との連携を早期に確立することを目的としています。

## [Nod Distributed Runtime](https://nod.ai/project/distributedruntime/): 非同期微細演算レベル並列ランタイム

Nod の MLIR ベース並列コンパイラと分散ランタイムは、微細な演算レベルの並列処理を活用しながら、
クラスター環境において CPU / GPU / アクセラレータ / FPGA といった多様な異種デバイス群にわたって、
非常に大規模なモデルの学習と推論を効率的にスケールアウトするための手法を提供します。

## [ONNX-MLIR](https://github.com/onnx/onnx-mlir)

ニューラルネットワークモデルを表現する際、ユーザーは機械学習の相互運用性を実現するオープン標準フォーマットである
[Open Neural Network Exchange (ONNX)](https://onnx.ai/onnx-mlir/) を使用することが一般的です。
ONNX-MLIR は MLIR ベースのコンパイラであり、ONNX 形式のモデルを
x86 アーキテクチャマシン、IBM Power Systems、IBM System Z など、
さまざまなターゲットハードウェア上で実行可能なスタンドアロンバイナリに変換する機能を提供します。

関連論文：[Compiling ONNX Neural Network Models Using MLIR](https://arxiv.org/abs/2008.08272)

## [OpenXLA](https://github.com/openxla)

コミュニティ主導のオープンソース機械学習コンパイラエコシステムで、
XLA と MLIR の優れた機能を統合しています。

## [PlaidML](https://github.com/plaidml/plaidml)

PlaidML は、さまざまなハードウェアターゲット (CPU、GPU、アクセラレータなど) において、
再利用可能な高性能な機械学習モデルを実現するテンソルコンパイラです。

## [PolyBlocks](https://www.polymagelabs.com/technology/#polyblocks): MLIR ベース JIT / AOT コンパイラ

PolyBlocks は、深層学習および非深層学習計算向けの高性能 MLIR ベースのエンドツーエンドコンパイラです。
JIT コンパイルとAOT コンパイルの両方に対応しています。
コンパイラエンジンは完全自動化、モジュール化、分析モデル駆動型を特徴としており、
ベンダー製 / HPC ライブラリへの依存を一切必要としない完全なコード生成機能を備えています。

## [Polygeist](https://github.com/llvm/Polygeist): MLIR 用 C/C++ フロントエンド・最適化ツール

Polygeist は、MLIR 向けの C/C++ フロントエンドであり、並列処理などの高抽象度なプログラム構造を保持します。
また、MLIR 向けの高度な最適化機能や、各種の抽象化 / ローワリングユーティリティも提供しています。

以下の論文を参照してください。
- [Polygeist: Raising C to Polyhedral MLIR](https://ieeexplore.ieee.org/document/9563011)
- [High-Performance GPU-to-CPU Transpilation and Optimization via High-Level Parallel Constructs](https://arxiv.org/abs/2207.00257)

## [Pylir](https://github.com/zero9178/Pylir)

Pylir は、高い言語仕様準拠性を備えた最適化付きの事前コンパイル型
Python コンパイラを目指して開発されています。
高抽象度で言語固有の最適化処理には MLIR 方言を採用し、
コード生成とガベージコレクションのサポートには LLVM を活用しています。

## [RISE](https://rise-lang.org/)

RISE は「アルゴリズムとハードウェア固有の最適化選択をエンコードする書き換え規則システムを備えた、
高抽象度の関数型データ並列言語」である [Liftプロジェクト](http://www.lift-project.org/) の精神的後継言語です。

## [SOPHGO TPU-MLIR](https://github.com/sophgo/tpu-mlir)

TPU-MLIR は、SOPHGO TPU 向けの MLIR ベースのオープンソース機械学習コンパイラです。
https://arxiv.org/abs/2210.15016

## [Substrait MLIR](https://github.com/substrait-io/substrait-mlir-contrib/)

Substrait MLIR は、データベースクエリプランの言語横断的なシリアライゼーション形式である
[Substrait](https://substrait.io/) (データベースクエリの中間表現 / IR に相当します)
向けの入出力方言です。

## [TensorFlow](https://www.tensorflow.org/mlir)

MLIR がグラフ変換フレームワークとして使用されるとともに、XLA、TFLite　コンバータ、
量子化処理など、数多くのツールを構築するための基盤技術として機能しています。

## [Tenstorrent MLIR Compiler](https://github.com/tenstorrent/tt-mlir)

tt-mlir は、Tenstorrent 社の AI アクセラレータ上での計算処理を抽象化するための
MLIR 方言を定義することを目的としたコンパイラプロジェクトです。
このプロジェクトは MLIR コンパイラ基盤の上に構築されており、TTNN をターゲットとしています。

本プロジェクトに関する詳細情報は、 https://tenstorrent.github.io/tt-mlir/ をご覧ください。

## [TFRT: TensorFlow Runtime](https://github.com/tensorflow/runtime)

TFRT は、非同期ランタイムシステム向けの統一的で拡張可能なインフラストラクチャ層を提供することを目的としています。

## [Torch-MLIR](https://github.com/llvm/torch-mlir)

Torch-MLIR プロジェクトは、PyTorch エコシステムから MLIR エコシステムへ、
一級コンパイラサポートを提供することを目的としています。

## [Triton](https://github.com/openai/triton)

Triton は、効率的なカスタム深層学習プリミティブを高度に記述するためのプログラミング言語およびコンパイラです。
本プロジェクトの目的は、 CUDA よりも高い生産性で高速なコードを記述できるオープンソース環境を提供するとともに、
他の既存 DSL と比較しても優れた柔軟性を実現することにあります。

## [VAST](https://github.com/trailofbits/vast): MLIR 用 C/C++ フロントエンド

VAST は、C/C++ および関連言語向けのプログラム解析・計測ライブラリです。
幅広い種類の解析に対応するカスタマイズ可能なプログラム表現の基盤を提供します。
MLIR インフラストラクチャを活用することで、
VAST はコンパイルプロセスの各段階において C/C++ プログラムを表現するためのツールセットを提供し、
さらにその表現を最適なプログラム抽象へと変換する機能を備えています。

## [Verona](https://github.com/microsoft/verona)

Project Verona は、並行所有権という概念を探求するための研究用プログラミング言語です。
所有権の概念をシームレスに統合した新しい並行処理モデルを提案しています。

## [Zaozi](https://github.com/sequencer/zaozi)

Zaozi は、[Chisel](https://chipsalliance/chisel)
を純粋な Scala 3 と MLIR で書き直すことを目的としたプロジェクトです。
Scala 3 上で軽量な eDSL を提供するとともに、MLIR の C-API を JVM プロジェクト Panama に統合します。
このプロジェクトの目的は、ハードウェア設計向けの eDSL フロントエンドフレームワークを提供することにあります。
