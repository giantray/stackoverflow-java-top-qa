Java 中如何将 String 转换为 enum
=======

###问题

###我有一个 enum 类

``` java 
public enum Blah {
    A, B, C, D
}
```
我想要找到一个 `String` 对应的 enum 值。例如, `"A"` 将是 `Blah.A`.如何做到?

我需要使用 `Enum.valueOf()` 方法吗? 如果是该如何使用?

---

### A1

是的, `Blah.valueOf("A")` 将会给你 `Blah.A`.

静态方法 `valueof()` 和 `values()` 在编译时期被插入,并不存在于源码中。但是在Javadoc中;例如,[`Dialog.ModalityType`](http://docs.oracle.com/javase/7/docs/api/java/awt/Dialog.ModalityType.html "Dialog.ModalityType")中显示了这两个方法。


### A2

另一个解答,如果文本和 `enumeration` 值不一致

``` java 
public enum Blah {
  A("text1"),
  B("text2"),
  C("text3"),
  D("text4");

  private String text;

  Blah(String text) {
    this.text = text;
  }

  public String getText() {
    return this.text;
  }

  public static Blah fromString(String text) {
    if (text != null) {
      for (Blah b : Blah.values()) {
        if (text.equalsIgnoreCase(b.text)) {
          return b;
        }
      }
    }
    return null;
  }
}
```
_评论区在讨论是应该返回null还是抛出异常,个人认为视具体情况,允许转换失败就返回null,不允许就抛出异常,或许创建一个特殊的空对象是个好的选择 -译者注_

### A3

这是我使用的一个工具类:

``` java 
/**
 * 一个对于所有Enum类通用的方法，因为他们不能有另一个基类
 * @param <T> Enum type
 * @param c enum type. All enums must be all caps.
 * @param string case insensitive
 * @return corresponding enum, or null
 */
public static <T extends Enum<T>> T getEnumFromString(Class<T> c, String string) {
    if( c != null && string != null ) {
        try {
            return Enum.valueOf(c, string.trim().toUpperCase());
        } catch(IllegalArgumentException ex) {
        }
    }
    return null;
}
```
之后,在我的enum类中通常如此使用来减少打字:
``` java 
public static MyEnum fromString(String name) {
    return getEnumFromString(MyEnum.class, name);
}
```
如果的enums不是全部大写,只需要修改 `Enum.valueOf` 这一行。
很遗憾，我不能使用 `T.class` 传给 `Enum.valueOf`,因为 `T`会被擦出。 

_评论区对于答主的异常处理一片指责 -译者注_

###A4
如果你不想编写自己的工具类,可以使用 Google的 `guava` 库:
``` java
Enums.getIfPresent(Blah.class, "A")
```
它让你检查是否 `Blan`中存在 `A`并且不抛出异常

_完整方法签名 `Optional<T> getIfPresent(Class<T> enumClass, String value)` , `Optional` 对象可以优雅的解决null值问题 -译者注_

---
_其他的答案都大同小异,感兴趣的可以看原帖_
stackoverflow链接
http://stackoverflow.com/questions/604424/lookup-enum-by-string-value
_译者:[MagicWolf](https://github.com/DaiDongLiang)_
