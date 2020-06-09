---
layout: post
title: Hello world in Elm
date: '2018-08-27 20:00:00 +0900'
tags: Elm Javascript
comments: true
---

# Elmを試してみたよ

[Elm](http://elm-lang.org/)という言語を知ったので試しに使ってみました。

Elmは、Haskellなどと同じ関数型言語でWikipediaによると『ユーザビリティ・パフォーマンス・堅牢性を重視して開発されている。静的かつ強力な型検査によって「事実上一切の実行時例外が起こらない」』ということを売りにしているようです。

# 関数型言語は慣れが必要

1,2年前に「[すごいHaskellたのしく学ぼう!](https://amzn.to/2MnAXam)」という本でHaskellをちょっとだけ学んだことがあったので、今回試したときはなんとか理解することができましたが、初めて関数型言語に触れる場合は前述の本やネット上の情報を少し見たほうが良いかもしれません。

# Hello world!

ということでさっそく「Hello world！」をElmで書いてみたいと思います。

## Elmをインストール

まずはElmのインストール。

Elmは、Mac・Windows・Linuxなどで使えるのですが、今回はnpmでインストールをして使ってみました。
（各環境のインストールは[こちらのガイド](https://guide.elm-lang.org/install.html)を見てみてください。）

とりあえず、プロジェクトのディレクトリを作ってそこにElmをインストールします。

```shell
$ mkdir hello_world
$ cd hello_world
$ npm init -y
Wrote to /PATH_TO/hello_world/package.json:
{
  "name": "hello_world",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
$ npm install elm
```

これでElmの開発環境のベースが出来ました。

## Elmのモジュールをインストール

次に、Elmの基本的なモジュール(core, html, virtual-dom)をインストールします。

```shell
$ npx elm package install elm-lang/html
Some new packages are needed. Here is the upgrade plan.

  Install:
    elm-lang/core 5.1.1
    elm-lang/html 2.0.0
    elm-lang/virtual-dom 2.0.4

Do you approve of this plan? [Y/n]
Starting downloads...

  ● elm-lang/html 2.0.0
  ● elm-lang/virtual-dom 2.0.4
  ● elm-lang/core 5.1.1

Packages configured successfully!
```

> 上記でしれっと`npx`というプログラムを使用していますが、それについて補足。npmではローカルインストールしたライブラリの実行ファイルはカレントディレクトリの`node_modules/.bin/`以下にインストールされます。最初はここに`$PATH`を通して使うのかな？と思ったのですが、調べてみるとnpmに付属するこの`npx`を使うとそれらの実行ファイルを実行することができるようです。今回はElmのインストール時にインストールされた`node_modules/.bin/elm`という実行ファイルを`npx`経由で実行しています。
> なおnpmとかnpxについてあまりわかってないので間違ってたら教えてください。

## プログラムを書く

これで開発の準備ができたので、以下のコードを`hello_world.elm`という名前で保存します。

```elm
import Html exposing (..)

main =
    text "Hello world!"
```

言わずもがなですが、これは「Hello world!」と表示するためのコードです。

1行目でHTML関連の関数が使えるように`Html`モジュールをインポートして、その後に続く`exposing (..)`で、`Html`モジュール内のすべての関数を使えるようにしています。

あとは、`main =`に続いて、`text "Hello world!"`と書きます。
これは`Hello world!`というテキストを表示するという意味です。

## Elm Reactorでプログラムを実行する

で、このプログラムを実行をしたいんですが、ElmにはReactorというツールが標準でついています。
このReactorを使うと、自前でビルドなどをすることなく簡単に作成したプログラムをビルドして実行することのできるツールです。

Elm Reactorをターミナルで起動します。

```shell
$ npx elm reactor
elm-reactor 0.18.0
Listening on http://localhost:8000
```

Elm Reactorを起動するとlocalhostの8000ポートにサーバが起動するので、ブラウザで`http://localhost:8000/`にアクセスをします。

そうすると下図のように起動したディレクトリのファイル一覧が表示されるので、その中の今回作成した`hello_world.elm`をクリックすると、プログラムに問題がなければ`Hello world!`と表示されるはずです。

![elm reactor image](/assets/2018-08-27/elm_reactor.png)

# おわり

とりあえず「Hello world!」が動きました。

ちょっと癖はありますが、やってて楽しい言語っぽい感じでした。
とはいえ、今回は関数型的な書き方は一切出てこなかったのでElmの魅力は伝わらなそうですね。
次回はその辺も使いつつもうちょっと有益なプログラムを書いてみたいと思います。
