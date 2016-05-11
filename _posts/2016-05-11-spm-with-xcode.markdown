---
layout: post
title: SPM with Xcode
date: '2016-05-11 09:19:00 +0900'
tags: Swift
---

# Swift 3.0をXcodeで開発したい！

ところで`main.swift`を書くときにXcodeは使えないのでしょうか。

Swiftに限りませんが最近の言語は補完などのIDEのサポートが使えないとけっこう書くのが大変ですよね。

でもご安心ください。最近のSPMにはすばらしいオプションが備わっています。

```
$ swift build --generate-xcode
```

このようにプロジェクトのルートディレクトで`--generate-xcode`オプション(`-X`でもOK)を付けて`swift build`をすることにより、プロジェクトのXcodeプロジェクトファイルを生成してくれるようになっています。

[前回のサンプル](/2016/05/10/using-other-libraries-in-swift-3-0/)で言うと`json-parser.xcodeproj`というファイルが生成されます。Xcodeでこのファイルを開けば、Xcodeで開発をすることができるようになります。

よーし、これでXcodeでもビルドや実行ができるようになる！…と思ったら落とし穴があります。

Xcodeは`swiftenv`の設定は読み込んでくれませんので、普通にビルドするとXcodeに標準でついているSwiftでビルドしてしまうのでビルドエラーになります。

そこで、Xcode側で使用するSwiftを切り替える必要がありますが、実は、Xcode7.3からその設定をPreferencesから簡単に行なえるようになっています。

> Preferences → Components → Toolchains

を開くと以下のようにSwiftのバージョンを変更する設定が表示されます。  
`swiftenv`でインストールしたSwiftのバージョンが自動的にここに表示されているはずです。

![Toolchains](/assets/2016-05-11/toolchains.png)

ここで使いたいSwiftを選択することで、Xcodeがビルドや実行をする際にそのSwiftを使うようになります。

Xcodeで開発をする際はこれを忘れずに設定しましょう。  
そして、通常の開発をするときは忘れずに設定を元に戻しましょう…。
