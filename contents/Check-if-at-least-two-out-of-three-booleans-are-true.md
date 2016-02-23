##给3个布尔变量，当其中有2个或者2个以上为true才返回true

###问题
给3个boolean变量，a,b,c，当其中有2个或2个以上为true时才返回true？
* 最笨的方法：
```java
boolean atLeastTwo(boolean a, boolean b, boolean c) 
{
    if ((a && b) || (b && c) || (a && c)) 
    {
        return true;
    }
    else
    {
        return false;
    }
}
```
* 优雅解法1
```java
    return a ? (b || c) : (b && c);
```

* 优雅解法2
```java
    return (a==b) ? a : c;
```

* 优雅解法3
```java
   return a ^ b ? c : a
```

* 优雅解法4
```java
    return a ? (b || c) : (b && c);
```


stackoverflow链接： http://stackoverflow.com/questions/3076078/check-if-at-least-two-out-of-three-booleans-are-true
