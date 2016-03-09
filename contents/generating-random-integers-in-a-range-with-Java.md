# java 产生指定范围的随机数
问题，如何使用 java 产生 0~10,5~10 之间的随机数？

## [Math.random()](http://docs.oracle.com/javase/8/docs/api/java/lang/Math.html#random--) 

Math.random() 可以产生一个 **大于等于** 0 且 **小于** 1 的双精度伪随机数，假设需要产生 ”0《= 随机数 <=10” 的随机数，可以这样做:

```
int num =(int)(Math.random() * 11);

``` 

那如何产生 “5 <= 随机数 <= 10” 的随机数呢?

```
int num = 5 + (int)(Math.random() * 6);

```

生成 “min <= 随机数 <= max ” 的随机数

```
int num = min + (int)(Math.random() * (max-min+1));

```


## [java.util.Random](http://docs.oracle.com/javase/8/docs/api/java/util/Random.html)

Random 是 java 提供的一个伪随机数生成器。

生成 “ min <= 随机数 <= max ” 的随机数：

```
import java.util.Random;

/**
 * Returns a pseudo-random number between min and max, inclusive.
 * The difference between min and max can be at most
 * <code>Integer.MAX_VALUE - 1</code>.
 *
 * @param min Minimum value
 * @param max Maximum value.  Must be greater than min.
 * @return Integer between min and max, inclusive.
 * @see java.util.Random#nextInt(int)
 */
public static int randInt(int min, int max) {

    // NOTE: Usually this should be a field rather than a method
    // variable so that it is not re-seeded every call.
    Random rand = new Random();

    // nextInt is normally exclusive of the top value,
    // so add 1 to make it inclusive
    int randomNum = rand.nextInt((max - min) + 1) + min;

    return randomNum;
}

```

## 标准库

在实际使用中，没有必要区重新写一次这些随机数的生成规则，可以借助一些标准库完成。如 [commons-lang](https://commons.apache.org/proper/commons-lang/index.html).

org.apache.commons.lang3.RandomUtils 提供了如下产生指定范围的随机数方法:

```
// 产生 start <= 随机数 < end 的随机整数
public static int nextInt(final int startInclusive, final int endExclusive);
// 产生 start <= 随机数 < end 的随机长整数
public static long nextLong(final long startInclusive, final long endExclusive);
// 产生 start <= 随机数 < end 的随机双精度数
public static double nextDouble(final double startInclusive, final double endInclusive);
// 产生 start <= 随机数 < end 的随机浮点数
public static float nextFloat(final float startInclusive, final float endInclusive);

```

org.apache.commons.lang3.RandomStringUtils 提供了生成随机字符串的方法，简单介绍一下:

```
// 生成指定个数的随机数字串
public static String randomNumeric(final int count);
// 生成指定个数的随机字母串
public static String randomAlphabetic(final int count);
// 生成指定个数的随机字母数字串
public static String randomAlphanumeric(final int count);

```

stackoverflow原址：http://stackoverflow.com/questions/363681/generating-random-integers-in-a-range-with-java
文章若有写得不正确或不通顺的地方，恳请你指出，谢谢。

