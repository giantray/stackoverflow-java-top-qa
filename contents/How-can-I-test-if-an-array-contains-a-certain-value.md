# 如何测试一个数组是否包含指定的值

指定数组，如:
```
public static final String[] VALUES = new String[] {"AB","BC","CD","AE"};
```
现在制定一个值 s，有哪些比较好的方式，判断这个数组 VALUES 是否包含值 s？

## 简单且优雅的方法:

1. Arrays.asList(...).contains(...)

2. 使用 Apache Commons Lang包中的ArrayUtils.contains


```java
String[] fieldsToInclude = { "id", "name", "location" };

if ( ArrayUtils.contains( fieldsToInclude, "id" ) ) {
    // Do some stuff.
}
```


## 自己写逻辑
问题的本质，其实是一个查找的问题，即查找一个数组是否包含某个值。对于原始类型，若是无序的数组，可以直接写一个 for 循环:
```
public static boolean useLoop(String[] arr, String targetValue) {
    for(String s: arr){
        if(s.equals(targetValue))
            return true;
    }
    return false;
}
```
若是有序的数组，可以考虑二分查找或者其他查找算法:
```
public static boolean useArraysBinarySearch(String[] arr, String targetValue) { 
    int a =  Arrays.binarySearch(arr, targetValue);
    if(a >= 0)
        return true;
    else
        return false;
}
```

若数组里包含的是一个个对象，实际上比较就是引用是否相等(String 类型是判断 值是否相等)，本质就是比较 hashcode 和 equal 方法，可以考虑使用 List 或者 Set，如下
```
public static boolean useList(String[] arr, String targetValue) {
    return Arrays.asList(arr).contains(targetValue);
}
```

```
public static boolean useLoop(String[] arr, String targetValue) {
    for(String s: arr){
        if(s.equals(targetValue))
            return true;
    }
    return false;
}
```

stackoverflow原址:http://stackoverflow.com/questions/1128723/how-can-i-test-if-an-array-contains-a-certain-value
