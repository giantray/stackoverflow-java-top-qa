##如何分割（split）string字符串
使用[`String#split()`](http://docs.oracle.com/javase/8/docs/api/java/lang/String.html#split-java.lang.String-)方法

如下所示：
```java
String string = "004-034556";
String[] parts = string.split("-");
String part1 = parts[0]; // 004
String part2 = parts[1]; // 034556
```
需要注意的是，该方法的参数是个[正则表达式](http://docs.oracle.com/javase/8/docs/api/java/util/regex/Pattern.html#sum),要注意对某些字符做转码。例如，`.`在正则表达式中表示任意字符，因此，如果你要通过`.`号做分割，需要这样写，`split("\\.")`或者`split(Pattern.quote("."))`

如果只是为了验证字符串中是否包含某个字符，使用[`String#contains`](http://docs.oracle.com/javase/8/docs/api/java/lang/String.html#contains-java.lang.CharSequence-)方法就行。注意该方法的参数，不是正则表达式

stackoverflow链接：
http://stackoverflow.com/questions/3481828/how-to-split-a-string-in-java
