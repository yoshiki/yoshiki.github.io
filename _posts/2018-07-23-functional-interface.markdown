---
layout: post
title: Functional Interface
date: '2018-07-23 18:00:00 +0900'
tags: Java Annotation
comments: true
---

# @FunctionalInterface

アノテーションを調べていたら`@FunctionalInterface`というアノテーションを知りましたので、これが何なのかちょっと調べてみました。

# tl;dr

結論から言うと`@FunctionalInterface`は、**抽象メソッド(Abstract method)を1つだけもつ関数型インターフェイスに付与されるアノテーション** のことだそうです。

これだけだとなんのこっちゃって感じですが、そういうものだそうです。
ちなみに`@FunctionalInterface`を付与したインターフェイスに抽象メソッドが2つ以上あるとコンパイル時に怒られます。

# 実際に使ってみる

たとえば以下のようなインターフェイスがあるとします。

```java
interface StringHandler {
    void handle(String str);
}
```

このインターフェイスは、`handle(String str)`という抽象メソッド（実装がない）が1つだけ定義されていますので、`@FunctionalInterface`を付けることができます。

```java
@FunctionalInterface
interface StringHandler {
    void handle(String str);
}
```

でも、なんで`@FunctionalInterface`を付ける必要があるのでしょうか。

それは、**このインターフェイスが、Java 8から追加されたラムダ式に使うことが出来る関数型インターフェイス(そのものずばりFunctional Interfaceですね)であることを明示するため** です。

実際、ラムダ式で良く使われるインターフェイス`Consumer`の実装を見ると`@FunctionalInterface`が付いてました。

```java
@FunctionalInterface
public interface Consumer<T> {

    void accept(T t);

    default Consumer<T> andThen(Consumer<? super T> after) {
        Objects.requireNonNull(after);
        return (T t) -> { accept(t); after.accept(t); };
    }
}
```

このインターフェイスには`andThen(Consumer<? super T> after)`という関数もありますが、これは`default`が付いているため、抽象メソッドではなく、デフォルトの実装がある関数のため抽象メソッドとは別になっていて、そのためこのインターフェイスは`@FunctionalInterface`だということになります。

ちなみに`Consumer`は、何かの値を受け取ってなにかの処理をするというシンプルなもので、以下のような使い方をします。

```java
// 与えられた値を二乗したものを出力する
Consumer<Integer> squareProcessor = integer -> {
    System.out.println(integer * integer);
};
squareProcessor.accept(100); // 10000
```

# 関数型インターフェイスの使い方

最初に紹介した`StringHandler`というインターフェイスは、String型を受け取ってなにかの処理をする関数を扱うインターフェイスになっていて、下記のように使うことができます。

```java
// 与えられた値を単純に出力する
StringHandler printHandler = str -> {
    System.out.println(str);
};
printHandler.handle("foo"); // foo
```

`printHandler`は`str`を受け取って、標準出力にその文字列を出力する処理になっています。

他にも下記のような感じでも使えますね。

```java
// 与えられた値を「:」で区切って、その最初の要素を出力する
StringHandler splitCommaHandler = str -> {
    String[] stuff = str.split(":");
    System.out.println(stuff[0]);
};
splitCommaHandler.handle("bar:baz:blah"); // bar
```

`splitCommaHandler`は、文字列を受け取って「:」で文字列を区切って、その最初の要素を標準出力に出す感じです。

このように何かを受け取って（なにも受け取らない場合もある）、何かの処理をするラムダ式やメソッド参照の代入先になれるインターフェイスのことを**関数型インターフェイス**といいます。


# 標準の関数型インターフェイスたち

## Consumerファミリー

Javaには先程ご紹介した`Consumer`以外にも、このような関数型インターフェイスが標準でいくつも用意されています。

例えば`IntConsumer`という関数型インターフェイスは、int型を受け取って何かをするインターフェイスです。
下記のような感じで使えます。

```java
// 与えられた値を2倍して出力する
IntConsumer doubleProcessor = num -> {
    System.out.println(num * 2);
};
doubleProcessor.accept(10); // 20
```

他にも、`LongConsumer`や`DoubleConsumer`などもあります。
<br>

あとは`BiConsumer`といって、引数を2つ(Bi)取って処理をする関数型インターフェイスもあります。
使い方は下記のような感じです。

```java
// 与えられた2つの値を掛け算して出力する
BiConsumer<Integer, Integer> multiplyProcessor = (num1, num2) -> {
    System.out.println(num1 * num2);
};
multiplyProcessor.accept(3, 4); // 12
```

## Supplierファミリー

`Consumer`系 以外にも、単純になにかを取得するための`Supplier`というインターフェイスもあります。

下記は単純に"foo"という文字列を取得する関数の例です。

```java
// fooという文字列を取得できる
Supplier<String> fooSupplier = () -> {
    return "foo";
};
String fooValue = fooSupplier.get(); // foo
System.out.println(fooValue);
```

もちろん`Supplier`には、`IntSupplier`や`LongSupplier`、`DoubleSupplier`などもあります。
<br>

## Functionファミリー

また、`Function`というのもあります。
これは、引数と返り値を定義することができる関数型インターフェイスです。

以下は`Integer`型を受け取って、それに"Hello, "を付加した`String`型の文字列を返す関数の例です。

```java
// 与えられた値に"Hello, "を付加して返す
Function<Integer, String> addHello = num -> {
    return "Hello, " + num;
};
String addHelloResult = addHello.apply(4949);
System.out.println(addHelloResult);
```


## Predicateファミリー

`Predicate`は何かを評価してその成否を扱うためのインターフェイスです。

以下は、与えられた文字列に"a"がはいっているかどうかを確認する処理の例です。

```java
Predicate<String> containsA = str -> {
    return str.contains("a");
};
if (containsA.test("hoge fuga")) {
    System.out.println("contains");
} else {
    System.out.println("not contains");
}
```

# andThen()って？

ちなみにご紹介した`Consumer`という関数型インターフェイスには`andThen()`という関数がデフォルト実装されていました。

これは、続けて処理をすることができる便利な機能のようです。

下記のように、最後の`accept()`で渡した値を、`doubleProcessor`→`trebleProcessor`→`quadrupleProcessor`の順に処理をするという使い方をすることができます。

```java
// 与えられた値を2倍して出力する
IntConsumer doubleProcessor = num -> {
    System.out.println(num * 2);
};

// 与えられた値を3倍して出力する
IntConsumer trebleProcessor = num -> {
    System.out.println(num * 3);
};

// 与えられた値を4倍して出力する
IntConsumer quadrupleProcessor = num -> {
    System.out.println(num * 4);
};

doubleProcessor
    .andThen(trebleProcessor)
    .andThen(quadrupleProcessor)
    .accept(5); // 10 15 20
```

関数型インターフェイスにはこのような便利な使い方ができるデフォルト実装があったりするので、興味があったら調べてみて下さい。

# おわり

ということで今回は`@FunctionalInterface`というアノテーションについて調べてみました。

今まであまりラムダ式とかを使ってなかったのでこれを機にもうちょっと使ってみようかと思いました。
