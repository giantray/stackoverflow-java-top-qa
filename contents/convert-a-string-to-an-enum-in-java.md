##如何将String转换为enum

### 问题
一个枚举定义：

```java
public enum Blah {
    A, B, C, D
}
```
并且我知道枚举的String值，比如 "A"，我想将其转换为Blah.A，我应该怎么做？  
是否有Enum.valueOf() 这样的方法，如果是，那我如何使用？


### 答案
是的，Blah.valueOf("A") 将会得到 Blah.A

静态方法valueOf() 和 values() 会在编译期创建，不过这不会体现在源代码内，他们出现在JavaDoc中，比如 [Dialog.ModalityTyp](http://docs.oracle.com/javase/7/docs/api/java/awt/Dialog.ModalityType.html) 中出现这两个方法。

### 其他答案

我有一个友善的工具方法：
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