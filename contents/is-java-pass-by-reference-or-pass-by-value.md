##Java是按值传递还是按引用传递

###概念定义
在开始之前，我们先理解按值传递、按引用传递的概念。
1. 什么是值传递？
指的是在方法调用时，传递的参数是按值的拷贝传递。按值传递重要特点：传递的是值的拷贝，也就是说传递后就互不相关了，**每个值都对应到一个地址空间**
2. 什么是引用传递
指的是在方法调用时，传递的参数是按引用进行传递，其实传递的引用的地址，也就是变量所对应的内存空间的地址。**传递的是值的引用，也就是说传递前和传递后都指向同一个引用（也就是同一个内存空间）**

###结论
明确地说，java都是按值传递的！java的世界里，都是按值传递。其实按值、按引用都只是个概念，关键是我们如何去理解java传参的本质。

###例子
下面就结合一个例子来说

有些情况下，所参数参数的值，是个“引用"，这一点容易让人产生困惑。

请看下面的例子

首先有如下代码
```java
Dog myDog = new Dog("Rover");  
foo(myDog);
```  
这时，你传给了foo函数一个参数，这个参数值，是个引用，也就是Rover这只dog的内存地址（这只是粗略的说明，因为在java中，这个地址并非是真正的地址）

假设这时候Rover的地址是42，那么，我们就是传了42这个地址给foo方法
```java
public void foo(Dog someDog) {  
    someDog.setName("Max");     // AAA  
    someDog = new Dog("Fifi");  // BBB  
    someDog.setName("Rowlf");   // CCC
}  
```
让我们逐步解析foo方法
1. 参数 someDog的值是一个地址（42）
2. 在AAA这一行：
someDog，也就是函数外声明的myDog，name从Rover被改成了Max
3. 在BBB这一行：
这就是关键的地方了，又new了一只dog，new 意味着又在新的地址空间放上了一只Dog，我们假设其地址是74。这时，someDog的值，会从42变成了74
4. 在CCC这一行：
这时候的修改，是对内存地址为74的那只狗的修改，而非原先的42

从这个例子，我们可以看到，foo方法的参数someDog，它是一个值，而非引用。如果它是引用，那么在foo方法内部的修改（包括BBB、CCC这两行），都应该会对42地址空间的dog产生影响，也就是方法外的参数，mydog也会指向新的地址空间。

###重要提醒
**再总结下Java初学者容易犯错的地方：**

假如你想像例子一样，传递一个对象到一个方法中，并由该方法修改对象。
- 可以成功修改a
```java
Object a = new Object();
foo(a)｛
a = new Object();
a.setXXX("XX");
｝
return a;//a实际未被修改
```

- 不可以成功修改a
```java
Object a = new Object();
foo(a)｛
a.setXXX("XX");
｝
return a;//此时的a已经是修改后的值
```

要切记，这个Object一定要在调用方法前，就初始化好（new一个），然后再作为参数传进去，并且在方法中不能再初始化这个参数。这样，在方法中对该参数的修改，才会有效地修改到a这个变量


stackoverflow链接
http://stackoverflow.com/questions/40480/is-java-pass-by-reference-or-pass-by-value