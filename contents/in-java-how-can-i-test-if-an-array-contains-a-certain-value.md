##如何判断数组Array是否包含指定的值

1. Arrays.asList(...).contains(...)

2. 使用 Apache Commons Lang包中的ArrayUtils.contains


```java
String[] fieldsToInclude = { "id", "name", "location" };

if ( ArrayUtils.contains( fieldsToInclude, "id" ) ) {
    // Do some stuff.
}
```

stackoverflow链接：
http://stackoverflow.com/questions/1128723/in-java-how-can-i-test-if-an-array-contains-a-certain-value