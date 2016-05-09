---
layout: post
title: "How to use hubot"
date: 2015-03-17 20:00
comments: true
categories: node hubot github coffeescript
---
Hubotというのを触ってみました。

# Hubotとは

[https://hubot.github.com/](https://hubot.github.com/)

Hubotはgithub謹製のbot開発用のフレームワークです。

coffeeescriptを使って書かれていて、node.js上で動かします。

Hubotの最大の特徴は、アダプタを使うことによって様々なサービス(hipchatとかslackとか)向けにbotを作ることができることです。

# インストール

OSXでのインストール方法です。

1. homebrewでnodeとredisをインストール

    まずは、nodeとredisをインストールします。

    redisは必須ではありませんが、あとでデータの永続化の際に必要なのでインストールしておきましょう。

    ```
    % brew install node redis
    ```

2. npmでhubotとcoffee-scriptをインストール

    次にhubotとcoffeescriptをインストールします。

    ```
    % npm install -g hubot coffee-script
    ```

3. npmでyoとgenerator-hubotをインストール

    次にyoとhubot用のジェネレータをインストールします。

    yoというのは[yeoman](http://yeoman.io/)というのは、モダンなWebアプリでよくあるジェネレータのためのフレームワークで、generator-hubotはそのyeomanのhubot用のジェネレータです。

    ```
    % npm install -g yo generator-hubot
    ```

4. yoを使って作りたいbotの雛形をジェネレートします。

    今回作るbotの名前を`hogebot`とします。

    ```
    % mkdir hogebot
    % cd hogebot
    % yo hubot
    ```

    `yo hubot`を実行するといくつかの質問を聞かれますので答えてください。
    Ownerはてきとう、Bot nameは今回は`hogebot`、Descriptionはてきとう、Bot adapterはbotを接続させたいサービスの名前にします(たとえば`hipchat`とか`slack`とか)。

    ちなみに対話形式がめんどくさい場合は、オプションで渡すこともできるようです。

    ```
    Usage:
      yo hubot:app [options]

    Options:
      -h,   --help         # Print generator's options and usage
            --owner        # Name and email of the owner of new bot (ie Example   user@example.com>)
            --name         # Name of new bot
            --description  # Description of the new bot
            --adapter      # Hubot adapter to use for new bot
            --defaults     # Accept defaults and don't prompt for user input
    ```

    全部の質問に答えるとごちゃごちゃ出力されますが気にしなくて大丈夫です。  
    関連するライブラリとかをインストールしているようです。

    で、処理が完了すると以下のようなhubotのためのファイル群がカレントディレクトリにできます。

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

5. hubotを起動してみる。

    この状態になるともうhubotを動かすことができます。
    bin/にhubotというのがあるのでそれを実行するとhubotが起動します。

    ```
    % bin/hubot
    ```

    これでhubotが起動します。

    ただ、これは単純にローカル(そのターミナル上)で起動しただけの状態で、`hipchat`とか`slack`につなげているわけではありません。
    ですが、この状態でも対話の確認をすることができます。

    以下のように`hogebot>`というプロンプトが出てくるので、そこに対してなにかをタイプするとそれが入力として扱われます。
    (※ちなみに`bin/hubot`を実行しただけだとプロンプトが見えない場合があるので、`hogebot>`が出てない場合は一度リターンキーを押した方がよいかもしれません)

    ```
    hogebot>
    ```

    試しにhubotに対してpingをしてみましょう。
    hubotに対して発言をする場合は、`hogebot: message`とか`@hogebot message`といった記法を使う必要があります。

    ```
    hogebot> hogebot: ping
    PONG
    hogebot>
    ```

    PONGと応答してくれましたね！

6. 終了させるには`exit`をタイプすればOK。

    ```
    hogebot> exit
    ```

    次にサービスにつなげてみましょう。

    今回はhipchatを例にしてみます。
    hipchatの場合は、接続情報を環境変数で設定する必要がありますがさきほど実行した`bin/hubot`をエディタで開いて以下のように書いておけばよさげです。

    各設定値は自分の使用する環境に合わせて適宜変更してください。

    ```
    #!/bin/sh

    set -e

    npm install
    export PATH="node_modules/.bin:node_modules/hubot/node_modules/.bin:$PATH"
    export HUBOT_HIPCHAT_JID="YOUR_BOT_JID"
    export HUBOT_HIPCHAT_PASSWORD="YOUR_BOT_PASSWORD"
    export HUBOT_HIPCHAT_HOST="YOUR_SERVICE_HOST"
    export HUBOT_HIPCHAT_XMPP_DOMAIN="YOUR_XMPP_DOMAIN"
    export HUBOT_HIPCHAT_ROOMS="YOUR_ROOM_NAMES"
    #export HUBOT_LOG_LEVEL="debug"

    exec node_modules/.bin/hubot --name "hogebot" "$@"
    ```

    設定ができたら実行してみます。

    今回はさきほどとは違い、`-a`オプションで接続するアダプターを指定します。下記の例ではhipchatに接続するようにアダプターを設定しています。

    ```
    % bin/hubot -a hipchat
    ```

    環境変数が正しければ、指定されたサービスの`HUBOT_HIPCHAT_ROOMS`で指定したルームにhogebotがジョインしているはずです。

    もし接続できていない場合は、環境変数が違うかルームの権限の問題で入れない等の理由が考えられるのでチェックしてみてください。

    さきほどはプロンプトに対して入力して対話をしましたが、今回は接続した先のルームで同じように`hogebot: ping`と発言してみましょう。ちゃんと接続できていればhogebotから`PONG`という返事がくるはずです。

---

以上駆け足になりましたが、hubotを起動する方法を紹介しました。

次回は、いろいろな返答をできるようにする方法をご紹介しようと思います。
