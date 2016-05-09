---
layout: post
title: "'isa' is deprecated in iOS7"
date: 2014-01-20 17:57
comments: true
categories: ios
---
iOS7 から foo->isa という記法が deprecated になっています。

こんなの普段は使わないので気にしなくてもいいと思っていたら JSONKit がこの記法を使っているんですね。

ってことで、JSONKit を使う場合には Stackoverflow の [iOS 7 : 'isa' is deprecated](http://stackoverflow.com/questions/19875166/ios-7-isa-is-deprecated) にあるような感じで解決することができます。

簡単に言うと、Podfile に以下のように書いておけばいいだけです。

```ruby
post_install do |installer_representation|
    installer_representation.project.targets.each do |target|
        target.build_configurations.each do |config|
            config.build_settings['CLANG_WARN_DIRECT_OBJC_ISA_USAGE'] = 'YES'
        end
    end
end
```

なにやってるかっていうと、対象のターゲットの Build Settings にある「Direct usage of 'isa'」を`YES`にするだけです。デフォルトでは`Yes(treat as error)`になっているので、isaを使っているとコンパイルエラーになってしまいます。

ってことで、CocoaPods で JSONKit 使ってるときはこれやると幸せになれます。

ちなみに CocoaPods 使ってなくても手動で Build Settings を変えれば同じ効果が得られます。
