---
layout: post
title: Using other libraries in Swift 3.0
date: 2016-05-10 19:00:00 +0900
tags: Swift
---

# 外部のライブラリを使ってみる

[前回](/2016/05/10/creating-swift-3-0-project/)は、SPMを使ってプロジェクトの雛形を作り、ビルドをして実行するところまでご紹介しましたが、プログラム的には`Hello, world!`を表示するだけの簡単なものでした。

これだけだとなにも面白くないので、今回は外部のライブラリを使ってもうちょっと複雑なものを作ってみたいと思います。

# プロジェクトの雛形を作る

前回と同じように雛形を作ります。今回はJSONをパースするプログラムを作りたいと思いますので、`json-parser`という名前で雛形を作成します。

```
$ mkdir json-parser && cd json-parser
$ swiftenv local DEVELOPMENT-SNAPSHOT-2016-05-03-a
$ swift build --init
Creating executable package: json-parser
Creating Package.swift
Creating .gitignore
Creating Sources/
Creating Sources/main.swift
Creating Tests/
```

# Package.swiftに依存するライブラリを追加する

自動生成された`Package.swift`は以下のようになっていると思います。

```swift
import PackageDescription

let package = Package(
    name: "json-parser"
)
```

ここに依存するライブラリを書くのですが、今回はJSONを扱うことのできる[ZewoプロジェクトのJSONライブラリ](https://github.com/Zewo/JSON)を使ってみようと思います。

`Package.swift`を以下のように書き換えてください。

```swift
import PackageDescription

let package = Package(
    name: "json-parser",
    dependencies: [
        .Package(url: "https://github.com/Zewo/JSON.git", majorVersion: 0, minor: 7),
    ]
)
```

追加されたのは`dependencies`以下3行です。

> `name:`の行の最後の`,`も忘れずに。

`.Package`の中に`url:`、`majorVersion:`、`minor:`というものが書いてあります。

これは、`url:`で指定したリポジトリにあるライブラリの、`0.7.X`のタグのソースをcloneしてきて使うという意味になります。`majorVersion:`が、`0.7.X`の0、`minor:`が7に対応しています。
一番右の数字は自動的に最新のものを探して適用してくれるようです。

`.Package()`にはいくつか書き方があるのですが、とりあえずはこの書き方を覚えておけばよいです。


# ビルドする

一旦この状態でビルドをしてみましょう。

```
$ swift build
Cloning https://github.com/Zewo/JSON.git
Resolved version: 0.7.0
Cloning https://github.com/Zewo/StructuredData.git
Resolved version: 0.7.0
Cloning https://github.com/open-swift/C7.git
Resolved version: 0.7.0
Compile Swift Module 'C7' (21 sources)
Compile Swift Module 'StructuredData' (1 sources)
Compile Swift Module 'JSON' (5 sources)
Compile Swift Module 'jsonparser' (1 sources)
Linking .build/debug/json-parser
```

前回のビルドとは違って、いろんなものが出力されました。

出力を見ればわかりますが、指定したJSONのライブラリとその依存ライブラリをcloneしてきてコンパイルされています。

> 余談ですが、JSONのリポジトリにも`Package.swift`があり、その中にJSONが依存しているライブラリが明記されています。

そして最後に今回作ろうとしている`json-parser`にリンクされて実行ファイルが作成されています。

このままでは、まだ`main.swift`は書いていないので、実行しても`Hello, world!`と出力されるだけです。

# プログラムを書く

では実際に`main.swift`でJSONライブラリを使ったプログラムを書いてみましょう。

`main.swift`に以下のようなコードを書いてください。

```swift
import JSON

let user = "{ \"name\": \"John\", \"age\": 20, \"hobby\": [ \"Football\", \"Baseball\" ] }"
let json = try JSONParser().parse(data: Data(user))


if let name = json["name"] {
    print("Name: \(name)")
}

if let age = json["age"] {
    print("Age: \(age)")
}

if let hobby = json["hobby"] where hobby.isArray {
    print("Hobby: \(hobby.array!)")
}
```

コードの内容は、JSONの文字列をパースして構造にしたものをそれぞれ出力するだけの簡単なものです。

# 再びビルドして実行する

では、ビルドして実行してみましょう。

```
$ swift build
Compile Swift Module 'jsonparser' (1 sources)
Linking .build/debug/json-parser
$ .build/debug/json-parser
Name: "John"
Age: 20
Hobby: ["Football", "Baseball"]
```

ちゃんと動いているようです。

Swift 3.0では、このように外部のライブラリを使うのもとても簡単にできます。

ですが、Swift 3.0対応のライブラリはまだまだ少なく、必要なものが出揃うのはもう少し時間が必要そうです。また、オープンソースで開発が進んでいるFoundation(のSwiftでの書き換え)もまだ時間がかかりそうなので、Pure Swiftだけでプログラムを書くのはけっこう大変ですが、今後がとても楽しみだなと思います。

今回は実際にコードを書いてビルドし、実行するところまでやってみましたがいかがだったでしょうか。

次回はSwift 3.0のプロジェクトでWebアプリを書いてみようと思います。
