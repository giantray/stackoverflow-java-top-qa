如何将枚举转换成数组
问题：
假设我有一个枚举类是这样的：
public enum Blah {
    A, B, C, D
}
我想要将枚举类的值转化成一个数组，比如"A"怎么可能是Blah.A.怎么有可能做到这点?我需要Enum.valueOf()这个方法吗？如果是这样，我应该怎么用他？

回答：
是的，Blah.valuOf("A")将会给你Blah.A。
valueOf()和values()这些静态方法是在编译时创建的，而且不会出现在源代码里。不过他们确实有出现在Javadoc,比如Dialog.ModalityType有这两个方法。
stackoverflow链接：
http://stackoverflow.com/questions/604424/lookup-enum-by-string-value
