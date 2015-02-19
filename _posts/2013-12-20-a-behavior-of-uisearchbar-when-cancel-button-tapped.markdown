---
layout: post
title: "iOS7でUISearchBarのキャンセルボタンを押したときの挙動"
date: 2013-12-20 13:36
comments: true
categories: ios
---
iOS7ではUISearchBarのデザインが変わって、ルーペとプレースホルダー(例えば「検索」という文字列)がセンタリングされるようになりました。

入力を開始されるとルーペとプレースホルダーが左端に移動して、そこで何かが入力された状態だとそのままの位置になりますが、何も入力されてない状態でキャンセルボタンを押すと、ルーペとプレースホルダーがまたセンターに移動するという仕様のようです。

で、こないだキャンセルボタンを押したときに以下のような実装をしていたら、ちょっと変な挙動になりました。

```
- (void)searchBarCancelButtonClicked:(UISearchBar *)searchBar {
    [searchBar setShowsCancelButton:NO animated:YES];
    [searchBar resignFirstResponder];
}
```

どんな挙動になったかというと、キャンセルボタンを押した瞬間にルーペだけが先にセンターに瞬間移動して、プレースホルダーだけがアニメーションしてセンターに移動する感じです。

図で表すと以下のような感じです(Ｑをルーペにみたててます)。

```
[Ｑ 検索　　　　　　　]
　　　　　↓
[ 　検索　Ｑ　　　　　]
　　　　　↓
[　　　Ｑ 検索　　　　]
```

他のアプリをみると、ルーペとプレースホルダーが一緒に動いてるのでなにか実装が違うんだろうなと思っていろいろ試してみたいら、なんのとこはない以下のように`resignFirstResponder`を先にすればよかっただけでした。

```
- (void)searchBarCancelButtonClicked:(UISearchBar *)searchBar {
    [searchBar resignFirstResponder];
    [searchBar setShowsCancelButton:NO animated:YES];
}
```

こうするとキャンセルボタンを押した際にルーペとプレースホルダーが一緒にセンターへ移動してくれるようになります。

