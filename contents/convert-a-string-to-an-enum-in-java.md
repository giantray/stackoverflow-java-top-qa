##如何将String转换为enum

### 问题
假设定义了如下的enum（枚举）：

```java
public enum Blah {
    A, B, C, D
}
```
已知枚举对应的String值，希望得到对应的枚举值。例如，已知"A"，希望得到对应的枚举——Blah.A，应该怎么做？  
Enum.valueOf()是否能实现以上目的，如果是，那我如何使用？


### 答案
是的，Blah.valueOf("A") 将会得到 Blah.A

静态方法valueOf() 和 values() 不存在于源码中，而是在编译时创建，我们也可以在JavaDoc查看到它们，比如 [Dialog.ModalityTyp](http://docs.oracle.com/javase/7/docs/api/java/awt/Dialog.ModalityType.html) 就中出现这两个方法。

### 其他答案

我有一个挺赞的工具方法：
```java
/**
 * A common method for all enums since they can't have another base class
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

你可以这么使用：

```java
public static MyEnum fromString(String name) {
    return getEnumFromString(MyEnum.class, name);
}
```

stackoverflow链接：http://stackoverflow.com/questions/604424/convert-a-string-to-an-enum-in-java