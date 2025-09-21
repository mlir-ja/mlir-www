---
title: "参加可能なプロジェクト"
date: 2019-11-29T15:26:15Z
draft: false
weight: 25
---

以下は [Google Summer of Code (GSOC)](https://summerofcode.withgoogle.com/) 、
あるいは単に MLIR に対するコントリビューションを始めるのに適しているプロジェクトのリストです。
バグトラッカーの [the "beginner" issues](https://github.com/llvm/llvm-project/issues?q=is%3Aopen+label%3Amlir%3Allvm+label%3Abeginner)
も一緒にご覧ください。
もしこの中で興味があるプロジェクトがあれば、気兼ねなく
[LLVM フォーラム](https://llvm.discourse.group/c/mlir/31) の MLIR セクション、あるいは
[LLVM discord](https://discord.gg/xS7Z362) の MLIR チャンネルで議論を行ってください。
メンターとして以下に記載している方は、
これらのプロジェクトを開始する際の最初の連絡先として参考にしてください。

* コア IR への C バインディングの実装。これにより、他の言語から IR を操作することができるようになります。
* llvm-canon のようなツールの MLIR 版 (メンター: Mehdi Amini, Jacques Pienaar)
* IR の調査を簡単にするための IR クエリツール (例えば、X が支配している全ての op、
  ある op から別の op への全ての経路など) (mentor: Jacques Pienaar)
* SPIR-V 方言に変換される GLSL フロントエンド (mentor: Lei Zhang)
  * 要件: SPIR-V 方言のグラフィック関連の機能の構築が必要
  * 目的: MLIR にフロントエンドをもっと作る :) グラフィック関連ツールの改善
  * 実世界での活用可能性: WebGL (GLSL でシェーダーが表現されている) から
	WebGPU (シェーダーが SPIR-V のような言語である [WGSL](https://gpuweb.github.io/gpuweb/wgsl.html)
	で表現されている) へのマイグレーションを解決する一案になる
* TableGen の「フロントエンド方言」 (mentor: Jacques Pienaar)
* MLIR での多面体スケジューリング (mentor: Alex Zinenko)
* MLIR 可視化 (mentor: Jacques Pienaar)
* MLIR スパース化 (つまりスパースコンパイラ) [starter tasks](https://github.com/llvm/llvm-project/labels/mlir%3Asparse) (mentor: Aart Bik)
* MLIR は複数の抽象化レベルを全てひとつの IR / 関数内で表すことができます。
  そのため MLIR モジュールの視覚化は、全て同じ抽象度のノードのグラフを表示する (この時点で自明ではない！) よりもはるかに複雑で、
  しかも機械学習用途には限定されません。
  MLIR モジュールの可視化に加え、MLIR そのものの可視化も重要です。
  特に、書き換え規則の可視化や、マッチの課程の可視化 (マッチ失敗も含む。 https://www.debuggex.com/ のような宣言的書き換え向け)、
  書き換えの影響を時間軸で考慮する、などです。
  可視化は全てオープンソースの部品で構成されるべきですが、
  スタンドアロンツールや (例えばオフラインの画像生成のための GraphViz など、そういったツールとの組み合わせ)、
  動的なツール (例えばブラウザでの表示) を用いても良いかは議論の余地があります。
  いずれの場合にせよ、オフラインで使えるようにすべきです。
  私たちは、幅広いアプローチの可能性を考慮して、関心のある学生と協力して、興味関心に基づいた正確なプロジェクトを改良していきます。
  また、この分野全般のプロポーザルも受け入れます。
* MLIR で表現された書き換えパターン (mentor: Jacques Pienaar)
* MLIR での汎用の値の範囲解析 (mentor: River Riddle)

### Projects started/starting soon:

This is section for projects that have not yet started but there are
individuals/groups intending to start work on in near future.

* [bugpoint/llvm-reduce](https://llvm.org/docs/BugpointRedesign.html) kind
  of tools for MLIR (mentor: Mehdi Amini, Jacques Pienaar)
* MLIR visualization, there are some projects in flight but we unfortunately
  don't know the project plans of those teams. But if you intend to work on
  something in this area it would be good to discuss on the forum early
  in case there are collaboration opportunity.

