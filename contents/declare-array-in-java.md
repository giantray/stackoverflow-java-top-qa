##在java中声明数组

###问题描述：
你是如何在Java中声明数组的。

###回答：
你可以直接用数组声明，或者通过数组的字面常量（array literal ）声明

对于原始类型（primitive types）：
```
int[] myIntArray = new int[3];
int[] myIntArray = {1, 2, 3};
int[] myIntArray = new int[]{1, 2, 3};
```

对于其他类，比如String类，也是相同的：
```
String[] myStringArray = new String[3];
String[] myStringArray = {"a", "b","c"};
String[] myStringArray = new String[]{"a", "b", "c"};
```
[stackoverflow链接：Declare array in Java?](http://stackoverflow.com/questions/1200621/declare-array-in-java)



