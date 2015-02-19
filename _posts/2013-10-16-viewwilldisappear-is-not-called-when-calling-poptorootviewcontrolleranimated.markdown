---
layout: post
title: "viewWillDisappear is not called when calling popToRootViewControllerAnimated"
date: 2013-10-16 16:23
comments: true
categories: ios
---
まぁ、表題の通りなんですが。

`UINavigationController`の`popToRootViewControllerAnimated:`を呼んだときに、そのとき表示していた`UIViewController`の`viewWillDisappear`が呼ばれるのを期待していると、実際には呼ばれないので残念なのことになります。

stackoverflowにも以下のようなスレがありました。

[viewWillDisappear not called when calling popToRootViewControllerAnimated](http://stackoverflow.com/questions/17954402/viewwilldisappear-not-called-when-calling-poptorootviewcontrolleranimated)

どうもiOSのバグっぽいことが書いてありますね。

対応としては、対象の`UIViewController`で`willMoveToParentViewController:`をオーバーライドするとそれで検知できるようです。

引数で渡ってくる`UIViewController`が`nil`の場合は、`popToRootViewControllerAnimated:`された時ということみたいです。
