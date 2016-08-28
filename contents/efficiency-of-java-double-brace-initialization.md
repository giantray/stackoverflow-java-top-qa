## "Double Brace Initialization"的效率问题

#### 问题

`Double Brace Initialization`是java的隐藏特性，它有着如下诱人的语法：

```java
Set<String> flavors = new HashSet<String>() {{
    add("vanilla");
    add("strawberry");
    add("chocolate");
    add("butter pecan");
}};
```

但是，这个特性听说不是很高效率，是否要限制一次性使用？

#### 回答

当我使用匿名内部类时出现了如下的问题：

```Auto
2009/05/27  16:35             1,602 DemoApp2$1.class
2009/05/27  16:35             1,976 DemoApp2$10.class
2009/05/27  16:35             1,919 DemoApp2$11.class
2009/05/27  16:35             2,404 DemoApp2$12.class
2009/05/27  16:35             1,197 DemoApp2$13.class

/* snip */

2009/05/27  16:35             1,953 DemoApp2$30.class
2009/05/27  16:35             1,910 DemoApp2$31.class
2009/05/27  16:35             2,007 DemoApp2$32.class
2009/05/27  16:35               926 DemoApp2$33$1$1.class
2009/05/27  16:35             4,104 DemoApp2$33$1.class
2009/05/27  16:35             2,849 DemoApp2$33.class
2009/05/27  16:35               926 DemoApp2$34$1$1.class
2009/05/27  16:35             4,234 DemoApp2$34$1.class
2009/05/27  16:35             2,849 DemoApp2$34.class

/* snip */

2009/05/27  16:35               614 DemoApp2$40.class
2009/05/27  16:35             2,344 DemoApp2$5.class
2009/05/27  16:35             1,551 DemoApp2$6.class
2009/05/27  16:35             1,604 DemoApp2$7.class
2009/05/27  16:35             1,809 DemoApp2$8.class
2009/05/27  16:35             2,022 DemoApp2$9.class
```

这是在我的一个简单应用中所产生的类信息。在这个应用中，使用了大量的匿名内部类，这些类会被单独地编译成`class`文件。

`Double Brace Initialization`是一个带有实例初始化块的匿名内部类。这就意味着每一个新的类的产生都会执行一次实例块，这样的目的通常是为了创建一个简单的对象。

java虚拟机在使用类之前需要去读取其classes信息，然后执行字节码校验等流程。所以为了保存这些`class`文件，所需要的磁盘空间会增大。

这个可以说是`Double Brace Initialization`的开销。所以尽量不要过分使用。

---

在java的介绍中，`Double Brace Initialization`有着如下的写法：

```java
List<String> list = new ArrayList<String>() {{
    add("Hello");
    add("World!");
}};
```

看起来像是java的隐藏特性，其实它只是下面代码的一个重写：

```java
List<String> list = new ArrayList<String>() {

    // 实例初始化块
    {
        add("Hello");
        add("World!");
    }
};
```

所以，它只是在匿名内部类中加上了实例初始化块而已。

---

Joshua Bloch希望以后的集合代码能够像这样简介：

```java
List<Integer> intList = [1, 2, 3, 4];

Set<String> strSet = {"Apple", "Banana", "Cactus"};

Map<String, Integer> truthMap = { "answer" : 42 };
```

但目前还没有这样的语法。

---

<strong>实践</strong>

做一个简单的试验：创建1000个带着`"Hello"`和`"World!"`元素的`ArrayList`

* 方法1：Double Brace Initialization

```
List<String> l = new ArrayList<String>() {{
  add("Hello");
  add("World!");
}};
```

* 方法2：初始化`ArrayList`并调用`add`方法

```java
List<String> l = new ArrayList<String>();
l.add("Hello");
l.add("World!");
```

我修改了java的源文件使之能够为每种上述方法分别创建出1000个实例

* 方法1的测试

```java
class Test1 {
  public static void main(String[] s) {
    long st = System.currentTimeMillis();

    List<String> l0 = new ArrayList<String>() {{
      add("Hello");
      add("World!");
    }};

    List<String> l1 = new ArrayList<String>() {{
      add("Hello");
      add("World!");
    }};

    /* snip */

    List<String> l999 = new ArrayList<String>() {{
      add("Hello");
      add("World!");
    }};

    System.out.println(System.currentTimeMillis() - st);
  }
```

* 方法2的测试

```java
class Test2 {
  public static void main(String[] s) {
    long st = System.currentTimeMillis();

    List<String> l0 = new ArrayList<String>();
    l0.add("Hello");
    l0.add("World!");

    List<String> l1 = new ArrayList<String>();
    l1.add("Hello");
    l1.add("World!");

    /* snip */

    List<String> l999 = new ArrayList<String>();
    l999.add("Hello");
    l999.add("World!");

    System.out.println(System.currentTimeMillis() - st);
  }
}
```

然后得出了下面的测试时间：

```Auto
Test1 Times (ms)           Test2 Times (ms)
----------------           ----------------
           187                          0
           203                          0
           203                          0
           188                          0
           188                          0
           187                          0
           203                          0
           188                          0
           188                          0
           203                          0
```

从上面我们可以看到，`Double Brace Initialization`平均时间花费了190ms左右。
同时，另外一种方法平均只用了0ms。

所以，在第一个方法测试程序中，即`Double Brace Initialization`产生了1000个`class`文件。

## StackOverflow地址

[http://stackoverflow.com/questions/924285/efficiency-of-java-double-brace-initialization](http://stackoverflow.com/questions/924285/efficiency-of-java-double-brace-initialization)