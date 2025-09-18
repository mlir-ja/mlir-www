---
date: 2017-10-19T15:26:15Z
lastmod: 2019-10-26T15:26:15Z
publishdate: 2018-11-23T15:26:15Z
---

# MLIR(Multi-Level Intermediate Representation) 概要

MLIR プロジェクトは、再利用性と拡張性の高いコンパイル基盤を構築する新たな取り組みです。
MLIRの狙いは、ソフトウェアの断片化への対処、ヘテロジニアスなハードウェア向けコンパイル方法の改善、
ドメイン固有コンパイラの構築コストの大幅な低減、そして既存のコンパイラ同士の接続を支援することです。

# 週次公開ミーティング

MLIR とエコシステムについて話す **週次の公開ミーティング** を開催しています。
次回のミーティング通知を受け取るには、Discourse の
[MLIR Announcements](https://discourse.llvm.org/c/mlir/mlir-announcements/44)
カテゴリを登録してください。

[この公開カレンダー](https://calendar.google.com/calendar/u/0?cid=N2EzMDU3NTBjMjkzYWU5MTY5NGNlMmQ3YjJlN2JjNWEyYjViNjg1NTRmODcxOWZiOTU1MmIzNGQxYjkwNGJkZEBncm91cC5jYWxlbmRhci5nb29nbGUuY29t)
を登録することで最新のスケジュールを確認できます。

あるトピックについて議論したい、あるいは質問がある場合は、
[アジェンダ](https://docs.google.com/document/d/1y2YlcOVMPocQjSFi3X6gYGRjA0onyqr41ilXji10phw/edit#)
に追加してください。

ミーティングの録画及びスライドは [講演・発表資料](talks/) を参照ください。


## 他の資料

MLIR についての他の資料は以下の通りです。

*   [LLVM forums](https://llvm.discourse.group/c/mlir/31) の MLIR セクションではあらゆる質問ができます。
*   [LLVM discord](https://discord.gg/xS7Z362) サーバーではリアルタイムで議論ができます。
*   以前の [講演・発表資料](talks/)

## MLIR の目的

MLIR は、複数の異なる用件を一つの基盤で満たせるような、
ハイブリッドな IR となることを意図して作られています。例えば、

*   TensorFlow にも見られるようなデータフローグラフを表現できる能力。動的 shape、
    ユーザー拡張可能な Op エコシステム、TensorFlow の変数を含む。
*   そのようなグラフ上で通常行われている最適化や変形 (例えば Grappler
    に見られるようなもの) 。
*   HPC スタイルのカーネルを跨いだループ最適化 (融合、交換、タイリング等)
    をホストし、データのメモリレイアウトを変形できる能力。
*   コード生成のための "lowering" 。例えば DMA 挿入、明示的なキャッシュ管理、
    メモリタイリング、1次元、2次元レジスタアーキテクチャ向けのベクトル化など。
*   例えばアクセラレータ固有の抽象度が高い演算など、
    ターゲット固有の操作を表現できる能力。
*   量子化やその他ディープラーニングで用いられているグラフ変形。
*   [多面体最適化](/docs/Dialects/Affine/).
*   [ハードウェア合成ツール / 高位合成](https://circt.llvm.org).

MLIR は共通の IR であり、ハードウェア固有の操作もサポートしています。
そのため、MLIR を取り巻くインフラ (例えばその上で動くコンパイラパス)
への投資は大きなリターンをもたらします。
多くのターゲットがそのインフラを利用でき、恩恵を受けられます。

MLIR は強力な表現ですが、目的としていないものもあります。
低レベルのマシンコード生成アルゴリズムをサポートしようとはしていません
(レジスタ割り当てや命令スケジューリング等) 。その用途には LLVM
などの低レベル最適化ツールの方が向いています。そして、MLIR をエンドユーザーが
CUDA C++ のようなカーネルを書くためのソース言語とすることも意図していません。
一方、MLIR はそのような DSL などを表現し、
エコシステムと統合するためのバックボーンを提供しています。

## Compiler infrastructure

We benefited from experience gained from building other IRs (LLVM IR, XLA HLO,
and Swift SIL) when building MLIR. The MLIR framework encourages existing
best practices, e.g. writing and maintaining an IR spec, building an IR verifier,
providing the ability to dump and parse MLIR files to text, writing extensive
unit tests with the [FileCheck](https://llvm.org/docs/CommandGuide/FileCheck.html)
tool, and building the infrastructure as a set of modular libraries that can be
combined in new ways.

Other lessons have been incorporated and integrated into the design in subtle
ways. For example, LLVM has non-obvious design mistakes that prevent a
multithreaded compiler from working on multiple functions in an LLVM module at
the same time. MLIR solves these problems by having limited SSA scope to reduce
the use-def chains and by replacing cross-function references with explicit
[`symbol reference`](docs/LangRef/#symbol-reference-attribute).

## Citing MLIR

Please see the [FAQ
entry](https://mlir.lemon.ski/getting_started/Faq/#how-to-refer-to-mlir-in-publications-is-there-an-accompanying-paper)
on how to cite MLIR in publications.
