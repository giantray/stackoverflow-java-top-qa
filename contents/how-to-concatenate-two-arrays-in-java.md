##如何便捷地将两个数组合到一起

###一行代码搞定
Apache Commons Lang library [`ArrayUtils.addAll(T[], T...)`](http://commons.apache.org/proper/commons-lang/javadocs/api-3.1/org/apache/commons/lang3/ArrayUtils.html#addAll%28T%5B%5D,%20T...%29)就是专门干这事的

代码：
```java
String[] both = ArrayUtils.addAll(first, second);
```

###不借助依赖包

####非泛型
把下面的`Foo`替换成你自己的类名
```java
public Foo[] concat(Foo[] a, Foo[] b) {
   int aLen = a.length;
   int bLen = b.length;
   Foo[] c= new Foo[aLen+bLen];
   System.arraycopy(a, 0, c, 0, aLen);
   System.arraycopy(b, 0, c, aLen, bLen);
   return c;
}
```

####泛型
```java
public <T> T[] concatenate (T[] a, T[] b) {
    int aLen = a.length;
    int bLen = b.length;

    @SuppressWarnings("unchecked")
    T[] c = (T[]) Array.newInstance(a.getClass().getComponentType(), aLen+bLen);
    System.arraycopy(a, 0, c, 0, aLen);
    System.arraycopy(b, 0, c, aLen, bLen);

    return c;
}
```
注意，泛型的方案不适用于基本数据类型（int，boolean……)