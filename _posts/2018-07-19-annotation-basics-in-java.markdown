---
layout: post
title: Annotation basics in Java
date: '2018-07-19 18:00:00 +0900'
tags: Java Annotation
---

# アノテーション

最近、Javaを書いてます。(そんなに書いてないけど…)

で、Javaには**アノテーション**という機能(？)がありますよね。
実際自分もいろんなところで使ってはいるんですが、ちゃんと理解しているわけじゃなかったので調べてみました。

# アノテーションとは

アノテーションとはJava SE 5で追加された機能でクラスやメソッド、パッケージに対してメタデータとして付加情報を記入する機能です。

簡単にいうとクラスやメソッド、パッケージに様々な意味をもたせることが出来るということのようです。

```java
public class SampleClass {

    @FooAnnotation // ←これとか
    public String text;

    @BarAnnotation("foo bar") // ←これとか
    public AnnotationSample() {
        //...
    }

    @BazAnnotation({ "foo", "bar" }) // ←これとか
    public void someMethod() {
        //...
    }

}
```

# アノテーションの種類

Javaのアノテーションは主に以下のようなものがあります。

- アノテーションが持つ値による分類
    - マーカーアノテーション
    値を持たず、名前だけを持つアノテーション
    例：`@Deprecated`, `@Override`
    - 単一値アノテーション
    値を1個だけ持つアノテーション
    例：`@SuppressWarnings("unused")`
    - フルアノテーション
    値を複数個持つアノテーション
    例：`@Target({ ElementType.METHOD, ElementType.CLASS })`
- その他
    - メタアノテーション
    アノテーションに付与するアノテーション

# アノテーションを自作する

アノテーションは自作することもできます。
作成するには`@interface`を使用します。

先にご紹介した各アノテーションを自作してみます。

## マーカーアノテーション

マーカーアノテーションの作成は簡単です。
以下のようにするだけで終わりです。

```java
public @interface SampleAnnotation {}
```

使用するには以下のようにするだけです。

```java
@SampleAnnotation // ←これ
public class SomeClass {
    //...
}
```

## 単一値アノテーション

次に単一値アノテーションを作成してみます。

String型の`value()`というのを定義すればいいみたいです。

```java
public @interface OneValueAnnotation {
    String value();
}
```

使用方法としては、以下のようになります。
引数で値を渡すことができます。

```java
@OneValueAnnotation("foo bar baz") // ←これ
public class SomeClass {
    //...
}
```

あと単一値アノテーションは、デフォルト値を設定することもできます。
デフォルト値を設定するには以下のように宣言のあとに`default "value"`と付け加えます。

```java
public @interface OneValueAnnotation {
    String value() default "foo";
}
```

こうすることで、値を書かない場合は`default`で設定した値がセットされるようです。

## フルアノテーション

フルアノテーションの場合は、以下のように2個以上の値を保持できるようにします。

```java
public @interface FullAnnotation {
    String value1();
    String value2() default "foo";
}
```

このアノテーションを使用する場合は以下のように名前を指定して値を渡します。

```java
@FulAnnotation(value1 = "foo", value2 = "bar")
public class SomeClass {
    //...
}
```

## メタアノテーション

メタアノテーションとは、アノテーションに付与するためのアノテーションです。
アノテーションに対してなにかの情報を付与することからアノテーションのアノテーションということで**メタ**アノテーションと呼ばれます。

後述する`@Target`や`@Retention`もメタアノテーションです。

メタアノテーションは、以下のように`@Target`を使用してアノテーションにのみ付与できるように定義します。

```java
@Target(ElementType.ANNOTATION_TYPE)
public @interface MetaAnnotation {}
```

`@Target`に`ElementType.ANNOTATION_TYPE`を指定することで、アノテーションにのみ使用するように制限します。

使い方は以下のようになります。

```java
@MetaAnnotation
public @interface NewAnnotation {}
```

もしアノテーション以外のところで指定すると以下のようなエラーになります。

*エラー: 注釈型はこの種類の宣言に使用できません*

# アノテーションの使用箇所の制限

アノテーションは、使用できる箇所を制限することができます。

制限をするには、先のメタアノテーションで軽く説明した`@Target`というアノテーションを使用します。使用できる箇所は、`ElementType`というEnumに定義されています。

| タイプ名 | 使用できる箇所 |
|:-|:-|
| ANNOTATION_TYPE | アノテーション型の宣言 |
| CONSTRUCTOR | コンストラクタの宣言 |
| FIELD | フィールドの宣言(enum定数を含む) |
| LOCAL_VARIABLE | ローカル変数の宣言 |
| METHOD | メソッドの宣言 |
| PACKAGE | パッケージの宣言 |
| PARAMETER | メソッドのパラメータの宣言 |
| TYPE | クラス・インタフェース・enum・アノテーションの宣言 |
| TYPE_PARAMETER | 型パラメータの宣言 |
| TYPE_USE | 型使用箇所 |

この中でよく使うのは、`CONSTRUCTOR`, `FIELD`, `METHOD` あたりでしょうか。

指定方法としては以下のようにすればOKです。

```java
@Target(ElementType.METHOD)
public @interface SampleAnnotation {}
```

複数指定することもできます。

```java
@Target({
        ElementType.METHOD,
        ElementType.CONSTRUCTOR,
        ElementType.FIELD
})
public @interface SampleAnnotation {}
```

なお、`@Target`に`METHOD`だけを指定したアノテーションをメソッド以外のところで指定したら以下のようなエラーになりました。

*エラー: 注釈型はこの種類の宣言に使用できません*


# アノテーションの値の保持期間

値の保持期間は`@Retention`というアノテーションで指定します。

指定できる値は`RetentionPolicy`というEnum型に定義があります。具体的には以下。

| タイプ | 保持期間 |
|:-|:-|
| CLASS | コンパイル時にクラスファイルに保持されますが、実行時にVMには保持されません。 |
| RUNTIME | コンパイル時にクラスファイルに保持され、さらに実行時にVMにも保持されます。<br>リフレクションを使って値を取り出せます。 |
| SOURCE | ソースファイルには保持されるが、コンパイルされたクラスファイルには保持されない。 |

値を実行時にも使いたい場合には`RUNTIME`が良いようですね。

指定方法としては以下のような感じ。

```java
@Retention(RetentionPolicy.RUNTIME)
public @interface SampleAnnotation {}
```

# アノテーションで保持した値を参照する

アノテーションで保持した値を参照するには、以下のようにするとできます。

```java
public class SampleClass {

    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.METHOD)
    public @interface SampleAnnotation {
        String value() default "Default value";
    }

    @SampleAnnotation("Passed value")
    public String getAnnotationValue() throws NoSuchMethodException {
        Method method = SampleClass.class.getDeclaredMethod("getAnnotationValue");
        SampleAnnotation sampleAnnotation = method.getDeclaredAnnotation(SampleAnnotation.class);
        return sampleAnnotation.value();
    }

    public static void main(String[] args) {
        try {
            SampleClass sampleClass = new SampleClass();
            System.out.println(sampleClass.getAnnotationValue());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

`@Retention`に`RetentionPolicy.RUNTIME`を指定していますが、これを`RetentionPolicy.CLASS`や`RetentionPolicy.SOURCE`にすると実行時に`Exception`が吐かれます。

このコードでは、実行時に値を参照できないといけないので`RetentionPolicy.RUNTIME`以外を指定するとエラーになります。

アノテーションに設定した値は`getAnnotationValue()`の中で取得しています。

# その他のアノテーションの設定

上記の`@Target`や`@Retention`以外にもアノテーションの挙動を設定するためのアノテーションがあります。

| 名前 | 用途 |
|:-|:-|
| `@Documented` | アノテーションを公開APIの一部としたい場合に使用。<br>簡単に言うと、javadocなどで作成されたドキュメントに指定されたアノテーションが載ります。 |
| `@Inherited`| これを指定されたアノテーションを使用したクラスのサブクラスにも継承したい場合に使用。 |

# おわり

ということで、アノテーションについての基本的なことを勉強がてらまとめてみました。

とりあえずアノテーションがどんなものかわかったので、次はもうちょっと実用性のあるアノテーションを作るにはどうしたらいいのかとか、標準であるアノテーションがどんなものがあるのかなどを調べてみます。
