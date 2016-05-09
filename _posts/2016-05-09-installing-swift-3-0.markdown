---
layout: post
title: Installing Swift 3.0
date: '2016-05-09 19:09'
---

![Swift](/assets/2016-05-09/swift.svg){:width="240ox"}

# 最近Swift 3.0をいじってます

現在AppleがリリースしているSwift(Xcodeに付属しているもの)は今2系ですが、現在別途3系の開発も進められています。

3系はSNAPSHOTとして公開されているので、ダウンロードして試すことができてSPM(Swift Package Manager)が使えたり、その他いろいろな新仕様が加えられたりします。

現状だと実用性という面ではまだまだですが、今後Webアプリなどの開発言語として採用される可能性もあるのでちょっとその辺も含めて触ってみようと思いました。

ということで、今回はSwift 3.0をインストールして試す環境を作る方法をご紹介します。

※Mac OS X(Xcodeインストール済)でのインストールを前提にしています(Ubuntuでもだいたい同じようにすればいけます)


# swiftenvのインストール

Swiftにもswiftenvなるツールがあります。

1つのマシンに複数バージョンの言語環境をインストールして切り替えることのできるツールです(plenvとかrbenvとかpyenvとかその辺のやつ)。

[https://github.com/kylef/swiftenv/](https://github.com/kylef/swiftenv/)

まずはこれをインストールします。

インストールは簡単で、cloneしたものを、`$HOME/.swiftenv`に入れるだけです。

```shell
$ git clone https://github.com/kylef/swiftenv.git ~/.swiftenv
```

あとは上の[githubのページ](https://github.com/kylef/swiftenv/)にあるように各シェル用にパスを通せば使えるようになります。

次に、Swift 3.0のスナップショットをインストール

# Swiftのインストール

次にSwiftをインストールします。

Swift 3.0はSNAPSHOTがswift.orgに公開されているのでそれをインストールするのですが、swiftenvの場合はそのファイル名(正確にはファイル名の一部)を指定するだけでインストールできます。

[https://swift.org/download/](https://swift.org/download/) の**Trunk Development (master)**のリンクにあるファイル名(の一部)をコピーしてswiftenvに渡せばOK。

最新のSNAPSHOTは 2016-05-03 に公開されたものがあるので、それを指定してみます。
インストールの途中でパスワードを聞かれますので入力をしてください。

{% highlight shell %}
$ swiftenv install DEVELOPMENT-SNAPSHOT-2016-05-03-a
Downloading https://swift.org/builds/development/xcode/swift-DEVELOPMENT-SNAPSHOT-2016-05-03-a/swift-DEVELOPMENT-SNAPSHOT-2016-05-03-a-osx.pkg
Password:
installer: Package name is Swift Open Source Xcode Toolchain
installer: Installing at base path /
installer: The install was successful.
DEVELOPMENT-SNAPSHOT-2016-05-03-a has been installed.
{% endhighlight %}

これだけでしばらく待てばインストールされます。

※ちなみにリンクのURLを渡してもインストールすることができますが、その場合はOS Xの場合はXcodeのやつを、Ubuntuの場合はUbuntuのやつを指定する必要があります。

# Swiftのバージョンを切り替える

この状態でSwiftのバージョンを確認すると以下のようになってるはずです。

```shell
$ swift --version
Apple Swift version 2.2 (swiftlang-703.0.18.8 clang-703.0.31)
Target: x86_64-apple-macosx10.9
```

これはXcodeのSwiftですね。

次に先程インストールしたバージョンがあるかどうかは、以下のようにすると確認できます。

```shell
$ swiftenv versions
  DEVELOPMENT-SNAPSHOT-2016-05-03-a
  latest
* 2.2 (set by /Users/JP10975/.swiftenv/version)
```

もしシステム全体でSwiftのバージョンを変えたい場合は、以下のようにします。

```shell
$ swiftenv global DEVELOPMENT-SNAPSHOT-2016-05-03-a
```

その後、再度 `swiftenv versions` を確認すると

```shell
$ swiftenv versions
* DEVELOPMENT-SNAPSHOT-2016-05-03-a (set by /Users/JP10975/.swiftenv/version)
  latest
  2.2
```

こうなっているはずです。実際にSwiftのバージョンを確認してみましょう。

```shell
$ swift --version
Apple Swift version 3.0-dev (LLVM dffa09ffd8, Clang 1e6cba3ce3, Swift d2aee43220)
Target: x86_64-apple-macosx10.9
```

切り替わっていますね。

でもglobalで変更してしまうと、いろいろと面倒なので通常はあるディレクトリ以下のみSwiftのバージョンを変えたい場合が多いでしょう。

その場合は以下のように`swiftenv local`というコマンドで変更することができます。

```shell
$ swiftenv local DEVELOPMENT-SNAPSHOT-2016-05-03-a
```

こうすると、そのディレクトリに`.swift-version`というファイルができ、そのディレクトリ以下のみ指定したバージョンのSwiftを使うことができるようになります。

ちなみに元のXcodeに付属するSwiftのバージョンに戻すには以下のようにすればOK。
※localのところは、場合によってはglobalを指定してください。

```shell
$ swiftenv local 2.2
```

ということで、今回はSwift 3.0のインストール方法とバージョンの切り替え方をご紹介しましたが、次回からはインストールしたものを使って簡単なアプリを作る方法をご紹介したいと思います(続くかな…)。
