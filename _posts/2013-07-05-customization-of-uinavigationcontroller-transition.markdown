---
layout: post
title: "Customization of UINavigationController transition"
date: 2013-07-05 18:54
comments: true
categories: 
---
UINavigationControllerのpushViewControllerの遷移の挙動(トランシジョン)は通常スライドしかできません。

もしそれ以外を使いたい場合は、CATransitionとかでやればいいのですが事前に用意されているのは以下の4つしかないようです。

    kCATransitionFade
    kCATransitionMoveIn
    kCATransitionPush
    kCATransitionReveal

そこで、もっといろんなトランシジョンを出来るようにするライブラリを作ってみました。

[YKNavigationControllerTransition](https://github.com/yoshiki/YKNavigationControllerTransition)

![YKNavigationControllerTransition](https://raw.github.com/yoshiki/YKNavigationControllerTransition/master/movie.gif)

使い方は、簡単に使う方法とより細かい制御ができる2つの方法を用意してあります。

## インストール方法 ##

* git clone してくる。
* YKNavigationControllerTransition/ というディレクトリを自分のプロジェクトに放り込む。
* 使いたいソースに以下を書いておけば準備オッケイ。

``` objective-c
#import "YKNavigationControllerTransition.h"
```

## 使い方 ##

大前提として、このライブラリはカテゴリで実装されてるのでself.navigationControllerをそのまま使うことができます。

### 簡単な方法 ###

``` objective-c
[self.navigationController pushViewController:viewController
                                     duration:0.3f
                          fromTransitionStyle:YKNavigationControllerTransitionStyleSlideOutLeft
                            toTransitionStyle:YKNavigationControllerTransitionStyleSlideInRight];
```

引数としては、pushするViewController、アニメーションする時間(duration)、遷移元のトランシジョン(fromTransitionStyle)、遷移先のトランシジョン(toTransitionStyle)の4つです。

上記で指定したトランシジョンの「YKNavigationControllerTransitionStyleSlideOutLeft」は、左側にスライドして出ていくトランシジョン。「YKNavigationControllerTransitionStyleSlideInRight」は、右側からスライドして入ってくるトランシジョンになります。

popする場合も同様に以下のように使うことができます。

``` objective-c
[self.navigationController popViewControllerWithDuration:0.3f
                                     fromTransitionStyle:YKNavigationControllerTransitionStyleSlideOutRight
                                       toTransitionStyle:YKNavigationControllerTransitionStyleSlideInLeft];
```

トランシジョンのスタイルは、以下の17個を用意してあります。

- YKNavigationControllerTransitionStyleIdentity  
なにもしない。動かない。

- YKNavigationControllerTransitionStyleFadeInBack  
後ろから拡大しながら出現する。

- YKNavigationControllerTransitionStyleFadeInFront  
前から縮小にながら出現する。

- YKNavigationControllerTransitionStyleFadeOutBack  
後ろに縮小にながら、薄くなりながら出ていく。

- YKNavigationControllerTransitionStyleFadeOutFront  
前に拡大しながら、薄くなりながら出ていく。

- YKNavigationControllerTransitionStyleSlideOutRight  
右側にスライドしながら出ていく。

- YKNavigationControllerTransitionStyleSlideOutLeft  
左側にスライドしながら出ていく。

- YKNavigationControllerTransitionStyleSlideOutTop  
上側にスライドしながら出ていく。

- YKNavigationControllerTransitionStyleSlideOutBottom  
下側にスライドしながら出ていく。

- YKNavigationControllerTransitionStyleSlideInRight  
右側からスライドしながら入ってくる。

- YKNavigationControllerTransitionStyleSlideInLeft  
左側からスライドしながら入ってくる。

- YKNavigationControllerTransitionStyleSlideInTop  
上側からスライドしながら入ってくる。

- YKNavigationControllerTransitionStyleSlideInBottom  
下側からスライドしながら入ってくる。

- YKNavigationControllerTransitionStyleRotateOutLeft  
左側にドアのように回転しながら出ていく。

- YKNavigationControllerTransitionStyleRotateOutRight  
右側にドアのように回転しながら出ていく。

- YKNavigationControllerTransitionStyleRotateInLeft  
左側からドアのように回転しながら入ってくる。

- YKNavigationControllerTransitionStyleRotateInRight  
右側からドアのように回転しながら入ってくる。

### よりカスタマイズできる方法 ###

上記の方法では表現できない、より複雑なことをしたい場合のために直接layerをいじってトランシジョンを表現するAPIも用意してあります。

``` objective-c
[self.navigationController pushViewController:viewController duration:0.3f preparations:^(CALayer *fromLayer, CALayer *toLayer) {
    toLayer.frame = CGRectOffset(toLayer.frame, toLayer.frame.size.width, 0.0f);
} animations:^(CALayer *fromLayer, CALayer *toLayer) {
    fromLayer.transform = CATransform3DMakeScale(0.9f, 0.9f, 1.0f);
    fromLayer.opacity = 0.0f;
    toLayer.frame = CGRectOffset(toLayer.frame, -toLayer.frame.size.width, 0.0f);
} completions:^(CALayer *fromLayer, CALayer *toLayer) {
    //
}];
```

こちらはBlockでfromLayerとtoLayerが渡ってくるので、それに対して処理をする形になります。

Blockは全部で3つ。

- preparations  
事前に処理したい場合  

- animations  
アニメーションの処理  

- completions  
トランシジョン完了時の処理

上記のコードは、「YKNavigationControllerTransitionStyleSlideOutLeft」と「YKNavigationControllerTransitionStyleSlideInRight」を指定したときと同じ処理になります。

popの場合も以下のように使うことができます。

``` objective-c
[self.navigationController popViewControllerWithDuration:0.3f preparations:^(CALayer *fromLayer, CALayer *toLayer) {
    toLayer.transform = CATransform3DMakeScale(0.9f, 0.9f, 1.0f);
    toLayer.opacity = 0.0f;
} animations:^(CALayer *fromLayer, CALayer *toLayer) {
    fromLayer.frame = CGRectOffset(fromLayer.frame, fromLayer.frame.size.width, 0.0f);
    toLayer.transform = CATransform3DIdentity;
    toLayer.opacity = 1.0f;
} completions:^(CALayer *fromLayer, CALayer *toLayer) {
    //
}];
```

### まとめ ###

このライブラリを使うと手軽にトランシジョンを変更することができるので興味があったら使ってみてください。

なお、まだ作ったばかりなのでバグとかありそうな気がするので、絶賛pull request募集中です。
