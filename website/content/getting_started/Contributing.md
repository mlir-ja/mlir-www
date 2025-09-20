---
title: "コントリビューションガイド"
date: 2019-11-29T15:26:15Z
draft: false
weight: 15
---

MLIR へのコントリビューションは誰でも歓迎しています。
バグ報告や、ドキュメントやチュートリアルの改善など様々な方法で関与しコントリビューションすることができます。

## コミュニティガイドライン

[LLVM Code of Conduct](https://llvm.org/docs/CodeOfConduct.html) に従い、
オープンで友好的な環境の育成を誓約してください。

### コードへのコントリビューション

GitHub に [プルリクエスト](https://llvm.org/docs/GitHub.html#github-reviews) を送ってください。
もしリポジトリへの書き込み権限が無い場合は、レビュアーにマージボタンを押すようコメントを残してください。

#### コミットメッセージ

Git の慣例に従いコミットメッセージを書いてください。
特に、最初の行はコミットの短いタイトルとなるようにしてください。
詳細を書きたい場合は、タイトルの後には空行を入れ、その後に書いてください。
コミットで何を変更したかよりも、 **なぜ** その変更を行ったかを書くことが推奨されています。
前者はコードから推測できる情報です。
この [記事](https://chris.beams.io/posts/git-commit/) に例と詳細が書かれています。

### イシュートラッキング

To report a bug, use the [MLIR product on the LLVM bug
tracker](https://github.com/llvm/llvm-project/issues/new), try to pick a
suitable component for the bug, or leave it in the default.

If you want to contribute, start working through the MLIR codebase, navigate to
[the "good first issue" issues](https://github.com/llvm/llvm-project/issues)
and start looking through interesting issues. If you decide to start on an
issue, leave a comment so that other people know that
you're working on it. If you want to help out, but not alone, use the issue
comment thread to coordinate.

### コントリビューションガイドライン・基準

*   [開発者ガイド](DeveloperGuide.md "ここ") を読んでください。
*   正しいライセンスを使用していることを確認してください。例は下にあります。
*   新しい機能を加える際はテストも含めてください。何故ならば、
    ①あなたのコードが正しく動いていることが証明できるため
    ②将来の破壊的変更への耐性を付けメンテナンスコストを下げるためです。
*   バグ修正も一般的にはテストを含めることが求められています。
    何故ならばバグが出るということは往々にしてテストカバレッジ不足を示しているからです。

#### ライセンス

新しいファイルの一番上にはライセンスを入れてください。

* [C/C++ ライセンス例](https://github.com/llvm/llvm-project/blob/main/mlir/examples/toy/Ch1/toyc.cpp)
