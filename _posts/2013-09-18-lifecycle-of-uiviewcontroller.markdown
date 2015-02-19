---
layout: post
title: "Lifecycle of UIViewController"
date: 2013-09-18 15:59
comments: true
categories: ios
---
UIViewControllerのライフサイクルは、もうそこかしこで語り尽されてると思うけど改めて書いてみる。

# 基本的なライフサイクル

コンストラクタ生成から非表示になるまでは、以下のようなメソッドを通ります(ARC前提で書いています)。

1. `init`
2. `initWithNibName:bundle:`
3. `loadView`
4. `viewDidLoad`
5. `viewWillAppear`
6. `viewDidAppear`
7. `viewWillDisappear`
8. `viewDidDisappear`
9. `dealloc`

# 各メソッドの呼び出しタイミング

基本的には上記のような感じなんですが、実際使うときにどんなタイミングで上記の各メソッドが呼ばれるかというと、以下のようになっています。

* コンストラクタ生成(`[ViewController new]`もしくは`[[ViewController alloc] init]`)  
  コンストラクタ生成するときは以下の順番でメソッドが呼ばれます。
  1. `init`
  2. `initWithNibName:bundle:`
* `view`メソッド(`[vc view]`もしくは`vc.view`)  
  vc(UIViewControllerのインスタンス)の`view`メソッドを呼ぶと以下の順番でメソッドが呼ばれます。よく`self.view`とか使われますが、この`view`というのはそのVCに`view`がなかった場合に生成するという意味もある。また、この際に呼ばれる`loadView`はその`view`を生成するメソッドになります。
  1. `loadView`
  2. `viewDidLoad`
* 表示(`pushViewController:animated:`、 `presentModalViewController:`)  
  生成されたvcを表示しようとすると以下のメソッドが呼ばれます。
  1. `viewWillAppear`
  2. `viewDidAppear`
* 非表示(`popViewController:animated:`、`dismissModalViewController:`)  
  逆にvcを非表示にする場合には以下のメソッドが呼ばれます。
  1. `viewWillDisappear`
  2. `viewDidDisappear`
  3. `dealloc`
  
この辺は覚えておくとなにかと役に立つと思うので、しっかり覚えておいた方がよさげです。

# おまけ：loadViewについての注意

先程`loadView`は、`view`を生成するためのメソッドであると書きましたが、このメソッドを自分でオーバーライドする場合には注意が必要です。もし`loadView`の中で自分で`self.view`を設定するか`[super loadView]`しないとその後に`self.view`にアクセスする毎に`loadView`が呼ばれることになり、無限ループに陥ります。

# おまけ2：loadViewの使い所

自分は基本的にコントローラーのビューはInterfaceBuilderを使わずにプログラムで生成しているのですが、そのビューの生成は`loadView`の中で生成することにしています。

``` objective-c
- (void)loadView {
    [super loadView];
    
    UIView *aView = [[UIView alloc] initWithFrame:self.view.frame];
    [self.view addSubview:aView];
    //...
}
```

`loadView`でビューを生成しているので、`viewDidLoad`ではビューが構築されていることを前提に各種ネットワークなどの処理(APIなどにアクセスしてデータを取ってくるなど)をさせています。ちなみに各ビューの位置調整は`viewWillAppear`で行なうことが多いです。

# おまけ3：deallocでよくやること

ARC環境でもちゃんと`dealloc`は呼ばれます。この`dealloc`は非ARC環境と違って基本的には何もしないメソッドなんですが、自分の場合はここに`NSLog(@"dealloc")`と書いておいたりします。

こうすると、そのコントローラーをpopなりdismissしたときにどこかで生き続けずにちゃんと破棄されているかどうかがわかるからです。循環参照とかになって破棄されずにいると、`dealloc`がログに出てこないのでそれで参照が残っているのが気付けたりします。

``` objective-c
- (void)dealloc {
    NSLog(@"dealloc");
}
```
