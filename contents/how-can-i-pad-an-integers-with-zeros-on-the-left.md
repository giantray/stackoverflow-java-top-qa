# 如何用0向左补齐一个整数？

## 问题

在Java中如何把一个整数转化为字符串并且用0向左补齐呢？

我基本上是希望把一个不大于9999的整数用0补齐 （比如说1=“0001”）。

## 解答

    String.format("%05d", yournumber);

可以将一个五位数用0补齐。


https://docs.oracle.com/javase/8/docs/api/java/util/Formatter.html

Stackoverflow链接：http://stackoverflow.com/questions/473282/how-can-i-pad-an-integers-with-zeros-on-the-left
