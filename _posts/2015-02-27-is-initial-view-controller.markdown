---
layout: post
title: "Where is an arrow for initial view controller"
date: 2015-02-27 20:00
comments: true
categories: ios
---
これからもっとブログを書こうと思っている次第。

Storyboard を使ってて、最初に開く View Controller を示す矢印ありますよね。

↓これです。

![](/assets/2015-02-27/arrow.png)

これを間違って消しちゃったときにどう復活させるか今日困ったんですが、同僚に教えてもらいました。

Xcode で当該の View Controller の Attributes Inspector に下記のようなところがあって、この「Is Initial View Controller」にチェックを入れるとその View Controller に矢印が付きます。

![](/assets/2015-02-27/is_initial_view_controller.png)

お試しあれ。


