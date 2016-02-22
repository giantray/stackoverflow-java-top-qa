# What is reflection, and why is it useful?

##问题描述
反射是什么，为什么它是有用的？
我特别感兴趣的是java，但我认为任何语言的原理都是相同的。

##回答
反射是被用来描述代码的，可以检查同一系统（或者自身）的其他代码。

举个例子，在java中你有一个不知道具体类型的对象，并且你可能会调用它的dosomething的方法（如果存在的话）java的静态类型系统并没有设计来支持此用法，除非对象符合已知的接口。但是用反射，你的代码能查看对象，并找出一个dosomething的方法（如果有的话），然后你可以调用他。
因此，一个例子如下（想象在问题中的对象foo）：
```
Method method = foo.getClass().getMethod("dosomething",null);
method.invoke(foo,null);  //调用foo的dosomething方法
```
java中一个非常常见的用例是使用注释。JUnit 4，举个例子，将使用反射来浏览你的标记有@test注释的类方法，之后当运行测试单元时进行调用。

[有很多好的反射例子，可以用来入门](http://docs.oracle.com/javase/tutorial/reflect/index.html)

最后，其概念在其他支持反射的静态类型语言中是非常相似的。在动态语言中，上面描述的用例并不是必要的（因为编译器允许任何方法都能被任何对象调用，在运行时如果不存在就会失败），但是第二种情况，寻找能以某种确定的方式工作的方法任然是常见的。

[原文链接：What is reflection, and why is it useful?](http://stackoverflow.com/questions/37628/what-is-reflection-and-why-is-it-useful)


