##为什么Math.round(0.49999999999999994)返回1？

###问题描述：

在下面的程序中你可以看到每个稍微比.5小的值都被向下舍入了，除了`0.5`那个。

```
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
输出
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
我使用 Java 6 update 31。

###回答：

**总结**

在Java 6中（而且很可能更早），`round x`以`floor(x+0.5)`的方式执行[1]。这是一个规则上的bug，它恰好会导致这样一种不合道理的情况[2]。Java 7不再授权这种分离的执行方式[3]。

**问题**

0.5+0.49999999999999994在双精度数中正好表示1：
```
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
这是因为0.49999999999999994有一个比0.5小的指数，所以它们相加时，它的尾数被移位了，ULP（ULP表示现有数值最后一位代表的最小数值单位，小于ULP的数值将无法累加到现有数值）变的更大。

**解决方法**

从Java 7开始，OpenJDK（如下例）这样执行它（round函数）[4]。
```
public static long round(double a) {
    if (a != 0x1.fffffffffffffp-2) // greatest double value less than 0.5
        return (long)floor(a + 0.5d);
    else
        return 0;
}
```
[1] http://docs.oracle.com/javase/6/docs/api/java/lang/Math.html#round%28double%29

[2] http://bugs.java.com/bugdatabase/view_bug.do?bug_id=6430675 (credits to @SimonNickerson for finding this)

[3] http://docs.oracle.com/javase/7/docs/api/java/lang/Math.html#round%28double%29

[4] http://grepcode.com/file/repository.grepcode.com/java/root/jdk/openjdk/7u40-b43/java/lang/Math.java#Math.round%28double%29


[stackoverflow链接：Why does Math.round(0.49999999999999994) return 1?](http://stackoverflow.com/questions/9902968/why-does-math-round0-49999999999999994-return-1)
