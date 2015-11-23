##在java中声明数组

###问题描述：
你是如何在java中声明数组的。

你可以进行用数组声明或者数组常量（注意：当你声明或者立即影响了变量，数组常量将不能再用来分配一个数组）
对于原始类型：
```
int[] myIntArray = new int[3];
int[] myIntArray = {1, 2, 3};
int[] myIntArray = new int[]{1, 2, 3};
```
物件类，比如String类，也是相同的：
```
String[] myStringArray = new String[3];
String[] myStringArray = {"a", "b","c"};
String[] myStringArray = new String[]("a", "b", "c");
```

注意:
1.数组常量
```
//for a function return type is int[],you cannot return
{1,2,3}
//but you can return 
new int[]{1, 2, 3};
```

[stack overflow?接：Declare array in Java?](http://stackoverflow.com/questions/1200621/declare-array-in-java#)



