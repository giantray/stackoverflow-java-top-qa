# 为什么数学函数Math.round(0.49999999999999994) 返回 1

tags:stackoverflow-java-top-qa

---
 
###问题
通过下面的程序你可以看出来,对于任意一个比0.5略小的都是舍去小数向下取整,只有0.5是例外.

```java
for (int i = 10; i >= 0; i--) {
    long l = Double.doubleToLongBits(i + 0.5);
    double x;
    do {
        x = Double.longBitsToDouble(l);
        System.out.println(x + " rounded is " + Math.round(x));
        l--;
    } while (Math.round(x) > i);
}
```

输出为:

```
10.5 rounded is 11
10.499999999999998 rounded is 10
9.5 rounded is 10
9.499999999999998 rounded is 9
8.5 rounded is 9
8.499999999999998 rounded is 8
7.5 rounded is 8
7.499999999999999 rounded is 7
6.5 rounded is 7
6.499999999999999 rounded is 6
5.5 rounded is 6
5.499999999999999 rounded is 5
4.5 rounded is 5
4.499999999999999 rounded is 4
3.5 rounded is 4
3.4999999999999996 rounded is 3
2.5 rounded is 3
2.4999999999999996 rounded is 2
1.5 rounded is 2
1.4999999999999998 rounded is 1
0.5 rounded is 1
0.49999999999999994 rounded is 1
0.4999999999999999 rounded is 0

```
*_译者注:请看输出的最后两行,0.49999999999999994的输出为1,而0.49999999999999999的输出为0*

我使用的版本是 Java 6 update 31

### 回答
**总结**

在 Java 6(或者之前的版本),round(x)是用floor(x+0.5)实现的.¹ 这是一个规范上的bug,恰恰是在这种病理条件下.²Java 7 不再使用这个有问题的实现了.

**问题**

0.5+0.49999999999999994 在double的精度下的结果是1
```java
static void print(double d) {
    System.out.printf("%016x\n", Double.doubleToLongBits(d));
}

public static void main(String args[]) {
    double a = 0.5;
    double b = 0.49999999999999994;

    print(a);      // 3fe0000000000000
    print(b);      // 3fdfffffffffffff
    print(a+b);    // 3ff0000000000000
    print(1.0);    // 3ff0000000000000
}
```
这是因为0.49999999999999994的指数比0.5的指数小,所以当它们两个相加时,0.49999999999999994的原数就会发生移位,然后最小精度单位(unit of least precision)/最后置单位(unit of last place)相应的变大了.

**解决方案**

自从Java 7以来,OpenJDK(举个栗子)实现如下⁴:

```java
public static long round(double a) {
    if (a != 0x1.fffffffffffffp-2) // greatest double value less than 0.5
        return (long)floor(a + 0.5d);
    else
        return 0;
}
```
1. [http://docs.oracle.com/javase/6/docs/api/java/lang/Math.html#round%28double%29](http://docs.oracle.com/javase/6/docs/api/java/lang/Math.html#round%28double%29)
2. [http://bugs.java.com/bugdatabase/view_bug.do?bug_id=6430675](http://bugs.java.com/bugdatabase/view_bug.do?bug_id=6430675)  (credits to @SimonNickerson for finding this)
3. [http://docs.oracle.com/javase/7/docs/api/java/lang/Math.html#round%28double%29](http://docs.oracle.com/javase/7/docs/api/java/lang/Math.html#round%28double%29)
4. [http://grepcode.com/file/repository.grepcode.com/java/root/jdk/openjdk/7u40-b43/java/lang/Math.java#Math.round%28double%29](http://grepcode.com/file/repository.grepcode.com/java/root/jdk/openjdk/7u40-b43/java/lang/Math.java#Math.round%28double%29)

### stackoverflow原文链接:[http://stackoverflow.com/questions/9902968/why-does-math-round0-49999999999999994-return-1](http://stackoverflow.com/questions/9902968/why-does-math-round0-49999999999999994-return-1)