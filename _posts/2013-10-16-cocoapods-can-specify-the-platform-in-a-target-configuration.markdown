---
layout: post
title: "CocoaPods can Specify the platform in a target configuration"
date: 2013-10-16 18:28
comments: true
categories: 
---
[CocoaPods](http://cocoapods.org/)、便利ですよねー。  
最近作られているiOSのライブラリは、CocoaPods対応が必須になってる風だったりします。

ただ、自分はまだまだ使いこなせてない部分がけっこうあって、こないだもCocoaPodsを使っていてハマったというか知らなかったことがあったのでメモっておきます。  
※なお、これから書く話はたぶん普通はみんな知ってる話だと思います…。

最近アプリのテストに[Kiwi](https://github.com/allending/Kiwi)というライブラリを使っています。

これ便利なんですけど、いつのころからかiOS 5.0以上対応になってしまって、iOS 4.3以上対応のアプリの場合使うことができません。

で、こないだまで強引にソースをダウンロードして使っていたんですが、調べてみたらPodfileの`target`の中で`platform`が使えるということがわかりました。

テスト用のライブラリってPodfileでは別ターゲットとして設定するんですが、その中だけ`platform`をiOS 5.0にしちゃえばKiwiを使うことができるんですね。

具体的には以下のようにすればよいようです。

```
platform :ios, '4.3'

pod 'AFNetworking', '~> 0.10.1'
pod 'JSONKit', :inhibit_warnings => true

target :SomeTests, :exclusive => true do
    platform :ios, '5.0'
    pod 'Kiwi', '2.2.2'
    pod 'OHHTTPStubs', '1.2.2'
end

```

テストに限っての話にはなってしまいますが`target`が別であれば、このように`target`の中だけ`platform :ios, '5.0'`と書くことで、Kiwiも使えるようになりました。

どうしてもiOS 4.3でテストを実行したいとかいう要件があるのであれば話は別ですが、ユニットテストだけであればこれで十分ですね。

ちなみに、Kiwiは2.2.1までは`s.ios.deployment_target = '5.0'`の記述がなかったので、iOS 4.3でも使えたのですが、どうしても2.2.2に上げる必要がでてきたため今回のような方法を発見できました。

なぜ2.2.2に上げなければならなかったのかというと「[Report failures for tested nil subjects properly](https://github.com/allending/Kiwi/pull/329)」この問題があったからです。

テスト対象のオブジェクトが`nil`の場合に変になっちゃうバグがあったようです。テストライブラリのバグはちょっと怖いですね…。

ってことで、Kiwiけっこういいですよ(ぇ。
