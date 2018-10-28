## 怎样得到指定名称的枚举类型对应的的字符串类型的枚举常量

### 问题：
假如说我有一个如下的枚举类
    
```java
    public enum Blah {
    A, B, C, D
    }
```
而我想要找出具有指定名称的枚举类型对应的的字符串类型的枚举常量，打个比方``"A"``对应的值为``Blah.A``。
此时我应该怎么做？
我是不是应该使用``Enum.valueOf()``这个方法？如果是的话，我又该怎么使用它？

### 回答：
是的，``Blah.valueOf("A")``将会给你你想要的``Blah.A``  
不过需要注意的是，你输入的名字必须是绝对匹配的，像``Blah.valueOf("a")``和``Blah.valueOf("A ")``都会抛出``IllegalArgumentException``
注：第一个表达式``a`` 与``A``不匹配
第二个表达式``A  ``后面多了一个空格

静态方法``valueOf()``和``values()``在编译时创建并且不会出现在编译后的源码里。但尽管如此，这两个方法还是确实出现在了Java文档里，[文档连接](https://docs.oracle.com/javase/7/docs/api/java/awt/Dialog.ModalityType.html)

stackoverflow讨论地址 [https://stackoverflow.com/questions/604424/how-to-get-an-enum-value-from-a-string-value-in-java](https://stackoverflow.com/questions/604424/how-to-get-an-enum-value-from-a-string-value-in-java)

    
