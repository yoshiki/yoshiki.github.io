---
layout: post
title: "How to use hubot"
date: 2015-03-17 20:00
comments: true
categories: node
---
Hubotっていうのを触ってみました。

#Hubotとは

[https://hubot.github.com/](https://hubot.github.com/)

Hubotはgithub謹製のbot開発用のフレームワークです。

coffeeescriptを使って書かれていて、node.js上で動かします。

Hubotの最大の特徴は、アダプタを使うことによって様々なサービス(hipchatとかslackとか)向けにbotを作ることができることです。

#インストール

OSXでのインストール方法です。

1. まずnodeとredisをHomeBrewでインストール

```% brew install node redis```  

2. npmでhubotとcoffee-scriptをインストール

```% npm install -g hubot coffee-script```

3. npmでyoとgenerator-hubotをインストール

yoというのは[yeoman](http://yeoman.io/)というのは、モダンなWebアプリでよくあるジェネレータのためのフレームワークです。  
generator-hubotは、そのyeomanのhubotのためのジェネレータです。

```% npm install -g yo generator-hubot```

4. yoを使って、作りたいbotの雛形をジェネレートします。

今回作るbotの名前を`hogebot`とします。

```
% mkdir hogebot
% cd hogebot
% yo hubot
```

`yo hubot`を実行するといくつかの質問を聞かれますが、Ownerはてきとう、Bot nameは`hogebot`、Descriptionはてきとう、Bot adapterは作りたいbotを使うサービスにします(たとえば`hipchat`とか`slack`とか)。

ちなみに対話形式がめんどくさい場合は、オプションで渡すこともできるようです。

```
Usage:
  yo hubot:app [options]
Options:
  -h,   --help         # Print generator's options and usage
        --owner        # Name and email of the owner of new bot (ie Example <user@example.com>)
        --name         # Name of new bot
        --description  # Description of the new bot
        --adapter      # Hubot adapter to use for new bot
        --defaults     # Accept defaults and don't prompt for user input
```

全部の質問に答えると、ごちゃごちゃいろいろと出力されますが関連するライブラリとかをインストールしているようです。
で、処理が完了するとhubotのためのファイル/ディレクトリ群がカレントディレクトリにできます。

```
% tree -L 1 -F
.
├── Procfile
├── README.md
├── bin/
├── external-scripts.json
├── hubot-scripts.json
├── node_modules/
├── package.json
└── scripts/
```

5.hubotを起動してみる。

bin/にhubotというのがあるのでそれを実行するとhubotが起動します。

```% bin/hubot```

これでhubotは起動しますが、これだけだとローカルで起動しただけです。`hipchat`とか`slack`につなげているわけではありません。
ですが、この状態でもプロンプトが出てくるので、hubotの挙動の確認ができます。
ちなみに`bin/hubot`を実行しただけだとプロンプトが見えないので、一度リターンを押した方がよいかも。

```
hogebot>
```

となっているので、pingをしてみるとPONGと応答が返ってきます。

```
hogebot> hogebot: ping
PONG
hogebot>
```

なんか応答してくれましたね！

6.終了させるには`exit`をタイプすればOK。

```
hogebot> exit
```

次にサービスにつなげてみましょう。
今回はhipchatを例にしてみます。
hipchatの場合は、接続情報を環境変数で設定する必要がありますが、さきほど実行した`bin/hubot`に以下のように書いておけばよさげです。
BOT\_JID、BOT\_PASSWORD、SERVICE\_HOST、XMPP\_DOMAIN、ROOM\_NAMEは適宜変更してください。

```
#!/bin/sh

set -e

npm install
export PATH="node_modules/.bin:node_modules/hubot/node_modules/.bin:$PATH"
export HUBOT_HIPCHAT_JID="BOT_JID"
export HUBOT_HIPCHAT_PASSWORD="BOT_PASSWORD"
export HUBOT_HIPCHAT_HOST="SERVICE_HOST"
export HUBOT_HIPCHAT_XMPP_DOMAIN="XMPP_DOMAIN"
export HUBOT_HIPCHAT_ROOMS="ROOM_NAME"
#export HUBOT_LOG_LEVEL="debug"

exec node_modules/.bin/hubot --name "hogebot" "$@"
```

次に実行してみます。今回はさきほどと違いオプションを指定します。

```% bin/hubot -a hipchat```

これはアダプターを指定しています。hipchatのアダプターを使ってくださいってことですね。

環境変数が正しければ、指定されたサービスに接続できるはずです。

さきほどはプロンプトに対して入力をしましたが、今回は接続した先のルームで同じように`hogebot: ping`などを発言すると、`PONG`と返事がくるはずです。

以上駆け足になりましたが、hubotを起動する方法を紹介しました。

---

次書けたら、いろいろな返答をできるようにする方法をご紹介しようと思います。
