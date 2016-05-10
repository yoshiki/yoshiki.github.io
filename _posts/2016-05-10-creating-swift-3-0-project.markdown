---
layout: post
title: Creating Swift 3.0 Project
date: 2016-05-10 13:51
---

# Swift 3.0でプロジェクトを作る

[前回の記事](/2016-05-09/installing-swift-3-0/)でSwift 3.0をインストールできたと思いますので、今回はそれを使ってプロジェクトを作成してみたいと思います。

Swift 3.0にはSwift Package Manager(以下SPM)というのが組み込まれていて、それを使うことによりプロジェクトの雛形を生成することができます。

# プロジェクトの雛形を作る

まず、プロジェクトのディレクトリを作ります。

```
$ mkdir helloworld
$ cd helloworld
```

次に、そのディレクトリ以下でSwift 3.0を使うようにswiftenvで設定をします。

```
$ swiftenv local DEVELOPMENT-SNAPSHOT-2016-05-03-a
```

これで準備ができました。それでは次にプロジェクトの雛形を生成してみます。SPMのコマンドである`swift build`というのを使います。

```
$ % swift build --init
Creating executable package: helloworld
Creating Package.swift
Creating .gitignore
Creating Sources/
Creating Sources/main.swift
Creating Tests/
```

プロジェクトを作るためのファイルが生成されました。カレントディレクトリに以下のようなディレクトリ/ファイルができたはずです。

```
.
├── Package.swift
├── Sources
│   └── main.swift
└── Tests
```

各ディレクトリ/ファイルがなんなのかちょっと説明します。

- Package.swift

    他の人が作ったライブラリなどをプロジェクトで使う場合にその依存関係を書くファイルです。

- Sources/

    プロジェクトのプログラムを入れるディレクトリです。

- Sources/main.swift

    プロジェクトのプログラムのメインとなるファイルです。
    このファイルにメインロジックを書きます。

- Tests/

    テストのファイルを入れるディレクトリです。

# ビルドする

この状態でプロジェクトをビルドしてみましょう。ビルドもSPMでできます。

```
$ swift build
Compile Swift Module 'helloworld' (1 sources)
Linking .build/debug/helloworld
```

ビルドができました。`.build/debug/helloworld`という実行ファイルができているはずです。

`main.swift`の中身を見るとわかるのですが、実は雛形で生成された`main.swift`には`Hello, world!`と表示するだけのプログラムが書いてあったので、生成された実行ファイルを実行すると、`Hello, world!`と表示されるはずです。

```
$ .build/debug/helloworld
Hello, world!
```

今回はプロジェクトの雛形を作ってビルドし、実行するまでをご紹介しました。実際やってみるとけっこう簡単だということがわかると思います。

次回は依存するライブラリを使ってプログラムを作る方法をご紹介しようと思います。
