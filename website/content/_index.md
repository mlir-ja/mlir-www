---
title: "ホーム"
date: 2017-10-19T15:26:15Z
lastmod: 2019-10-26T15:26:15Z
publishdate: 2018-11-23T15:26:15Z
---

# MLIR(Multi-Level Intermediate Representation) 概要

MLIR プロジェクトは、再利用性と拡張性の高いコンパイラ基盤を構築する新たな取り組みです。
MLIRの狙いは、ソフトウェアの断片化への対処、ヘテロジニアスなハードウェア向けコンパイル手法の改善、
ドメイン固有コンパイラの構築コストの大幅な低減、そして既存のコンパイラ同士の接続を支援することです。

# 週次公開ミーティング

MLIR 及びそのエコシステムについて話す **週次の公開ミーティング** を開催しています。
次回のミーティング通知を受け取るには、Discourse の
[MLIR Announcements](https://discourse.llvm.org/c/mlir/mlir-announcements/44)
カテゴリを登録してください。

[この公開カレンダー](https://calendar.google.com/calendar/u/0?cid=N2EzMDU3NTBjMjkzYWU5MTY5NGNlMmQ3YjJlN2JjNWEyYjViNjg1NTRmODcxOWZiOTU1MmIzNGQxYjkwNGJkZEBncm91cC5jYWxlbmRhci5nb29nbGUuY29t)
を登録することで最新のスケジュールを確認できます。

あるトピックについて議論したい、あるいは質問がある場合は、
[アジェンダ](https://docs.google.com/document/d/1y2YlcOVMPocQjSFi3X6gYGRjA0onyqr41ilXji10phw/edit#)
に追加してください。

ミーティングの録画及びスライドは [講演・発表資料](talks/) を参照してください。


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
*   [多面体最適化](/docs/Dialects/Affine/)。
*   [ハードウェア合成ツール / 高位合成](https://circt.llvm.org)。

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

## コンパイラ基盤

MLIR の構築にあたっては、LLVM IR、XLA HLO、Swift SIL などの他の IR
構築の経験が役に立ちました。MLIR は、既存のベストプラクティスを歓迎しています。
例えば、IR の仕様記述やメンテナンス、IR 検証ツールの構築、
MLIR ファイルをテキストにダンプやパースができる能力の提供、
[FileCheck](https://llvm.org/docs/CommandGuide/FileCheck.html)
による拡張性の高いユニットテストの記述、
そしてモジュール式のライブラリを新方式で組み合わせ基盤をビルドすることです。

その他の教訓も、設計に巧妙に盛り込まれ、統合されています。
例えば LLVM には、マルチスレッドのコンパイラが LLVM 
モジュール中の複数の関数を同時に処理できなくするような非自明な設計ミスがあります。
MLIR では use-def チェーンを減らすために SSA スコープを制限し、
複数の関数にわたる参照を明示的な [`symbol reference`](docs/LangRef/#symbol-reference-attribute)
に置き換えることで解決しています。

## MLIR を引用するには

MLIR を出版物で引用する方法については、
[FAQ](https://mlir.lemon.ski/getting_started/Faq/#how-to-refer-to-mlir-in-publications-is-there-an-accompanying-paper)
をご覧ください。
