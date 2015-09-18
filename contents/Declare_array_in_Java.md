##在 Java 里定义一个数组

###问题
如何在 Java 里定义一个数组？

###回答
你可以只是声明一个数组，也可以用数组初始化的形式（但是只有在声明并马上赋值的时候才可以用这种方式，数组初始化的形式并不能用于对数组进行再次赋值）

对于 基本类型 数组来说：
```java
int[] myIntArray = new int[3];
int[] myIntArray = {1,2,3};
int[] myIntArray = new int[]{1,2,3};
```

对于 类 来说，比如String，以下这几种的方式是一样的：
```java
String[] myStringArray = new String[3];
String[] myStringArray = {"a","b","c"};
String[] myStringArray = new String[]{"a","b","c"};
```

stackoverflow原址：
http://stackoverflow.com/questions/1200621/declare-array-in-java