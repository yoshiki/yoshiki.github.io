---
layout: post
title: "UIKit Dynamicsを試してみた"
date: 2013-12-19 17:28
comments: true
categories: ios
---
iOS7 SDKから新たに追加されたUIKit Dynamicsというのを使ってみたのでご紹介。

UIKit Dynamicsは簡単に言うと、UIKitのビューたちを簡単に物理的に動かすことができるようになるAPIです。

使い方もとても簡単で、`UIDynamicAnimator`というビューを動かすためのクラスに対して、用意されたBehaviorクラスを設定させてやるだけで、ビューを動かすことができます。

例えば、あるView Controllerの`self.view`上にある`targetView`というビューを、`point`で示すポイントに吸い付かせるように動かすコードは以下にように3行で書けます。

```
CGPoint point = CGPointMake(100.0f, 100.0f);
UIDynamicAnimator *animator = [[UIDynamicAnimator alloc] initWithReferenceView:self.view];
UISnapBehavior *snapBehavior = [[UISnapBehavior alloc] initWithItem:targetView snapToPoint:point];
[animator addBehavior:snapBehavior];
```

もともと用意されているBehaviorクラスは以下の6つです。

- `UIAttachmentBehavior`  
ある場所を起点にビューを動かすことのできるBehavior
- `UICollisionBehavior`  
画面の枠や他のビューとの衝突を管理することのできるBehavior
- `UIDynamicItemBehavior`  
ビューの特性を変えることのできるBehavior
- `UIGravityBehavior`  
ビューに重力を与えることのできるBehavior
- `UIPushBehavior`  
ビューを押したような効果を与えることのできるBehavior
- `UISnapBehavior`  
ビューをある点に吸着させたような効果を与えることのできるBehavior

これらのBehaviorクラスは1つずつでも使えますが、いくつかを組み合わせて使うこともできます。

ちなみにBehaviorは独自にも実装できます。  
`UIDynamicItem`プロトコルに則ったクラスを作ればいいみたいです。

ってことで、これらでちょっと遊んでみたので、その時のコードをgithubに上げておきました。  
実行するとUIKit Dynamicsがどんなものかわかると思います。

[DynamicsDemo](https://github.com/yoshiki/DynamicsDemo)

あとサンプルコードを実行したら動画も上げておきます。

<iframe width="420" height="315" src="//www.youtube.com/embed/y1zABWDG9ic" frameborder="0" allowfullscreen></iframe>
