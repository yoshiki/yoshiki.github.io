---
layout: post
title: "UITableViewのreorder時のドラッグ開始/終了のイベントを取得する"
date: 2013-04-24 17:35
comments: true
categories: 
---
UITableViewの並び替えでセルを「ドラッグしたとき」と「ドロップしたとき」のイベントを取る必要があって調べてみたのですが、公式にはそのようなイベントハンドラがないようです(プライベートAPIにはあるとかないとか)。

そこで調べてみたら、UITableViewCellにUILongPressGuestureRecognizerを仕込んで、reorderのコントロールを掴んだ時と離した時をUITableViewにdelegateしてやるとそれっぽいことができるというのを知ったのでやってみました。

参考にしたのは、StackOverFlowの「[How to get notified of UITableViewCell move start and end](http://stackoverflow.com/questions/9898388/how-to-get-notified-of-uitableviewcell-move-start-and-end)」という記事

## 先にやり方をまとめ ##

- UITableViewCellのサブクラスを作って、UILongPressGuestureRecognizerをreorderのコントロールに仕込む
- 上で仕込んだイベントが発生したら、UITableViewにその状態をdelegateする
- UITableView側でその状態を保持するインスタンス変数を用意して、そいつに状態を保持させて、ドラッグ中なのかどうかを判定する

## 実装 ##

実装は以下のようになっています。最終的にはMyTableViewControllerのisDraggingTableViewCellのYES/NOでドラッグ中かどうかが判定できます。

``` objective-c MyTableViewController.h
#import <UIKit/UIKit.h>

// MyTableViewCellDelegate のプロトコルを設定
@interface MyTableViewController : UITableViewController <UITableViewDelegate, UITableViewDataSource, MyTableViewCellDelegate>

@end
```

``` objective-c MyTableViewController.m
@interface MyTableViewController ()

// ドラッグ状態保持用のインスタンス変数を定義
@property (nonatomic, assign, getter = isDraggingTableViewCell) BOOL draggingTableViewCell;

@end

@implementation

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    // ...

    if (cell == nil) {
        cell = [[MyTableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier];
        cell.delegate = self; // delegateを設定
    }

    // ...
}

// reorderさせるためにこのdelegateメソッドを定義
- (void)tableView:(UITableView *)tableView moveRowAtIndexPath:(NSIndexPath *)sourceIndexPath toIndexPath:(NSIndexPath *)destinationIndexPath {
    // do anything
}

#pragma mark - MyTableViewCellDelegate

// ドラッグ状態を取得するためのdelegateメソッドを定義
- (void)tableViewCell:(MyTableViewCell *)cell dragging:(BOOL)dragging {
    // 渡ってきたドラッグ状態を保持する
    _draggingTableViewCell = dragging;
}

@end
```

``` objective-c MyTableViewCell.h
#import "MyTableViewCell.h"

@class MyTableViewCell;

@protocol MyTableViewCellDelegate <NSObject>

// delegateメソッドの定義
- (void)tableViewCell:(MyTableViewCell *)cell dragging:(BOOL)dragging;

@end

@interface MyTableViewCell : UITableViewCell

@property (unsafe_unretained) id<MyTableViewCellDelegate> delegate;

@end
```

``` objective-c MyTableViewCell.m
#import "MyTableViewCell.h"

@implementation MyTableViewCell

- (void)setEditing:(BOOL)editing animated:(BOOL)animated {
    [super setEditing:editing animated:animated];

    if (editing) {
        // subviewsの中からreorderのコントロールをみつけて、UILongPressGestureRecognizerを設定
        for (UIView * view in self.subviews) {
            NSString *viewClassName = NSStringFromClass([view class]);
            if ([viewClassName isEqualToString:@"UITableViewCellReorderControl"]) {
                if (view.gestureRecognizers.count == 0) {
                    UILongPressGestureRecognizer *gesture =
                        [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(handleGesture:)];
                    gesture.cancelsTouchesInView = NO;
                    gesture.minimumPressDuration = 0.15;
                    [view addGestureRecognizer:gesture];
                }
            }
        }
    }
}

// ドラッグ状態をdelegateに送信
- (void)handleGesture:(UIGestureRecognizer *)gestureRecognizer {
    if (gestureRecognizer.state == UIGestureRecognizerStateBegan) {
        [self.delegate tableViewCell:self dragging:YES];
    } else if (gestureRecognizer.state == UIGestureRecognizerStateEnded) {
        [self.delegate tableViewCell:self dragging:NO];
    }
}

@end
```

