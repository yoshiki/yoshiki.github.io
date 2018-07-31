---
layout: post
title: Dynamic Proxies
date: '2018-07-30 10:00:00 +0900'
tags: Java Dynamic Proxies
comments: true
---

# 動的プロキシとは

あるメソッドが呼ばれた際にその前後でなにかの処理を挟む手法をAOP(アスペクト指向プログラミング)といいますが、それを実現するために必要な動的プロキシという機能がJavaにあるようです。

今回は、JavaSEに標準である`java.lang.reflect.Proxy`を利用して動的プロキシを試してみました。

# やること

今回は、`SampleClass`の`someMethod()`を呼んだ際に、その前後でメッセージを出力するものを実装します。

# 必要なクラスやインターフェイス

## SampleClass

これは`someMethod()`という抽象メソッドを定義した単純なインターフェイスになっています。

```java
public interface SampleClass {
    void someMethod();
}
```

## SampleClassImpl

`SampleClass`インターフェイスの実装を`SampleClassImpl`クラスとして実装します。

`SampleClassImpl`クラスには単純なメッセージを出力するだけの`someMethod()`が実装されています。

```java
public class SampleClassImpl implements SampleClass {

    @Override
    public void someMethod() {
        System.out.println("someMethod process");
    }
}
```

## MyInvocationHandler

`MyInvocationHandler`クラスは、`InvocationHandler`インターフェイスの実装をしています。

```java
public class MyInvocationHandler implements InvocationHandler {

    Object target;

    public MyInvocationHandler(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("--- Begin invoke");
        Object ret = method.invoke(target, args);
        System.out.println("--- End invoke");
        return ret;
    }
}
```

このクラスでは、**差し込みたい処理** （と元の処理の実行）が実装されています。
差し込みたい処理とは、今回は元々の処理の前後に`--- Begin invoke`と`--- End invoke`というメッセージを出力する処理になります。

`InvocationHandler`では`invoke(Object proxy, Method method, Object[] args)`という関数を実装する必要があって、この中で`method.invoke(target, args)`を実行することで元の処理が行われるます。つまりその前後でなにかやりたいときは、`method.invoke(target, args)`の前後にその処理を書けばよいということです。

> ちなみに`method.invoke(target, args)`の`method`は元々呼ばれた関数、`target`は元のクラスのインスタンス、`args`は元々呼ばれた関数の引数になっています。

## Main

最後はメインの処理が実装されている`Main`クラスです。

```java
public class Main {

    public static void main(String[] args) {
        SampleClass sampleClass = (SampleClass) Proxy.newProxyInstance(
                SampleClass.class.getClassLoader(),
                new Class[]{ SampleClass.class },
                new MyInvocationHandler(new SampleClassImpl())
        );
        sampleClass.someMethod();
    }

}
```

重要なのは、`Proxy.newProxyInstance()`の部分です。
ここで動的プロキシを作っています。

この`Proxy.newProxyInstance()`は、指定したインターフェイス（SampleClass）のメソッドが呼ばれた際に、その処理を`MyInvocationHandler`クラス（後述）に委譲するようなインスタンスを返してくれます。

そして、動的プロキシを作った後に`sampleClass.someMethod()`を実行していますが、これは`someMethod()`が呼ばれるのではなく、先に説明した`MyInvocationHandler`の`invoke(Object proxy, Method method, Object[] args)`が呼ばれるという仕組みです。

# 実行結果

上記のプログラムを実行すると以下のように出力されます。

```
--- Begin invoke
someMethod process
--- End invoke
```

ちゃんと`someMethod()`の実行前後に`--- Begin invoke`と`--- End invoke`が出力されていますね。

処理の流れとしては以下のようになっています。

1. `Proxy.newProxyInstance()`で動的プロキシのインスタンスを生成。
2. `sampleClass.someMethod()`をコール（実際の`SampleClass#someMethod()`は呼ばれない）。
3. `MyInvocationHandler.invoke()`が呼ばれる。
4. `--- Begin invoke`が出力される。
5. `method.invoke(target, args)`で元々の関数`SampleClass#someMethod()`が呼ばれて`someMethod process`が出力される。
6. `--- End invoke`が出力される。

# おわり

今回は、動的プロキシをJavaSEの標準機能で実装してみました。

これを覚えるといろいろできそうな気になりますが、この辺はすでにあるAOPのライブラリを使えばもっと簡単に実装できるようなので（まだよくわかってない）あとでそちらを見てみたいと思います。
