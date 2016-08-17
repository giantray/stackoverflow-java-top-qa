## Java内部类和嵌套静态类

### 问题
Java 当中的内部类和静态嵌套类有什么主要区别? 在这两者中有什么设计或者实现么?

### 回答
嵌套类分为两类: 静态和非静态. 用`static`装饰的嵌套类叫做静态类, 非静态的嵌套类叫做内部类.

静态嵌套类使用外围类名来访问:
```java
OuterClass.StaticNestedClass
```
例如, 实例化一个静态嵌套类的对象就要使用这种语法:
```java
OuterClass.StaticNestedClass nestedObject = new OuterClass.StaticNestedClass();
```

内部类对象的存在需要依靠一个外部类的对象. 看看下面的类:
```java
class OuterClass {
    ...
    class InnerClass {
        ...
    }
}
```
内部类对象只有当外部类对象存在时才有效, 并且可以直接访问他的包裹对象(外部类对象)的方法以及成员.

因此, 要实例化一个内部类对象, 必须先实例化外部类对象. 然后用这种语法来创建内部类对象:
```java
OuterClass.InnerClass innerObject = outerObject.new InnerClass();
```
参考: [Java Tutorial - Nested Classes](http://download.oracle.com/javase/tutorial/java/javaOO/nested.html)

提醒一下, 还有一种不用外部类对象来创建内部类对象的方法: [inner class without an enclosing ](http://stackoverflow.com/questions/20468856/is-it-true-that-every-inner-class-requires-an-enclosing-instance)
```java
class A {
  int t() { return 1; }
  static A a =  new A() { int t() { return 2; } };
}
```
在这里, `new A() { ... }`是一个定义在静态上下文的内部类对象, 并没有一个外围对象.

stackoverflow链接: [Java inner class and static nested class](http://stackoverflow.com/questions/70324/java-inner-class-and-static-nested-class)
