# 反射（reflection）是什么及其用途?

## 问题描述
反射是什么，为什么它是有用的？
我特别感兴趣的是java，但我认为任何语言的原理都是相同的。

## 回答
反射的概念，主要是指程序可以访问、检测和修改它本身状态或行为的一种能力。在java中，通过反射，能够在"运行态"动态获得任意一个类的所有属性和方法，动态地调用对象的方法。

举个例子，假设你有一个不知道具体类的对象，并且你想调用它的"dosomething"方法（如果存在的话）。java的静态类型系统只能调用一个已知类对象对应的已知接口，在未指定对象类型时，无法调用它的方法。但是通过反射，你的代码能检查这个未知类对象，并试图找出这个dosomething方法。如果存在这个方法，你可以通过反射调用这个方法。

为了进一步说明，请看下面的例子（下面的对象foo，就是上文提到的，我们不知道它对应的类是什么）：
```
Method method = foo.getClass().getMethod("dosomething",null);
method.invoke(foo,null);  //调用foo的dosomething方法
```
反射这个特性，经常会用于各种注解中(annotations)。举个例子，Junit4将使用反射来遍历你的代码，查找所有加了@test注解的类方法，之后运行测试单元时就调用这些方法。

[有很多好的反射例子，可以用来入门](http://docs.oracle.com/javase/tutorial/reflect/index.html)

最后，其概念在其他支持反射的静态类型语言中也是非常相似的。在动态语言中，无需用到上面说的第一种用法场景——调用未知类的方法（因为动态语言编允许任意对象调用任意方法，如果不存在对应方法，在运行时就会失败），但是第二种情况，查找做了指定标记的方法，这种场景还是很常见的

[stackoverflow链接：What is reflection, and why is it useful?](http://stackoverflow.com/questions/37628/what-is-reflection-and-why-is-it-useful)


