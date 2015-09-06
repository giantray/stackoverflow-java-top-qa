## JavaBean 到底是什么？

### 问题

按照我的理解： “Bean” 是一个带有属性和getters/setter方法的Java类。它是不是和C的结构体是相似的呢，对吗？
一个“Bean"类与普通的类相比是不是语法的不同呢？还是有特殊的定义和接口？
为什么会出现这个术语呢，这让我很困惑？
如果你很好心告诉我一些关于`Serializable`接口的信息，对于你的答案那到底是什么意思，我会非常感谢你的。

### 回答

JavaBean 只是一个[标准](http://www.oracle.com/technetwork/java/javase/documentation/spec-136004.html)

1. 所有的属性是私有的（通过用[getters/setters](https://en.wikipedia.org/wiki/Mutator_method)）
2. 一个公有的无参数的构造器
3. 实现了[序列化](http://docs.oracle.com/javase/8/docs/api/java/io/Serializable.html)

就这些，它只是一个规范。但是很多的类库都是依赖于它。

对于`Serializable`,看一下[API文档的解释](http://docs.oracle.com/javase/8/docs/api/java/io/Serializable.html)

```
实现java.io.Serializable接口的类能串行化。
不实现此接口的类不会有任何状态的序列化和反序列化。
可序列化类的所有子类型本身都是可序列化。
序列化接口没有方法或字段，仅用于标识的可序列化的语义。
```
换句话说，序列化的对象可以被写入流，文件，对象数据库等。

另外一个JavaBean类和一个普通的类没有语法区别，如果遵循上面的标准的话，一个类可以定义成JavaBean类。

之所以有JavaBean是因为这个标准允许类库编译完成任务通过你预定方式定义的类实例。举个例子，如果一个类库想要流你通过的任何对象，它知道它可以因为你的对象是可序列化的。（假设这个类库需要你的对象是合适的JavaBeans）

**stackoverflow链接**：
http://stackoverflow.com/questions/3295496/what-is-a-javabean-exactly

### 关于序列化相关博客


1. [我对Java Serializable（序列化）的理解和总结](http://xiebh.iteye.com/blog/121311)
2. [理解Java对象序列化](http://www.blogjava.net/jiangshachina/archive/2012/02/13/369898.html)
