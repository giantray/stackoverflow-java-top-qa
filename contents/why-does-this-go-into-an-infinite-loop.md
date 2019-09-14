## 这段代码为什么陷入了死循环

### 问题
我写了这样一段代码

```java
public class Tests {
    public static void main(String[] args) throws Exception {
        int x = 0;
        while(x<3) {
            x = x++;
            System.out.println(x);
        }
    }
} 
```

我们知道他应该只写x++或者x=x+1就行了，但是x=x++的情况下，x应该先赋值给自己，然后再增加1。为什么X的值一直是0呢？

--更新
</br>下面是字节码

```java
public class Tests extends java.lang.Object{
public Tests();
  Code:
   0:   aload_0
   1:   invokespecial   #1; //Method java/lang/Object."<init>":()V
   4:   return

public static void main(java.lang.String[])   throws java.lang.Exception;
  Code:
   0:   iconst_0
   1:   istore_1
   2:   iload_1
   3:   iconst_3
   4:   if_icmpge   22
   7:   iload_1
   8:   iinc    1, 1
   11:  istore_1
   12:  getstatic   #2; //Field java/lang/System.out:Ljava/io/PrintStream;
   15:  iload_1
   16:  invokevirtual   #3; //Method java/io/PrintStream.println:(I)V
   19:  goto    2
   22:  return

}
```


### 回答

一开始我用C#代码来解释,因为C#允许用"ref"关键字来实现int参数的引用传递,所以我决定用我google到的MutableInt类写的标准的java代码来模拟C#中ref关键字作用.我不知道这对我的解答是不是有帮助，我并不是一个专业的java开发者，我知道还有很多更符合语言习惯的方法去解释这个问题。
也许我写一个函数来模拟x++的作用会解释得更清楚

```java
public MutableInt postIncrement(MutableInt x) {
    int valueBeforeIncrement = x.intValue();
    x.add(1);
    return new MutableInt(valueBeforeIncrement);
}
```

对不对？将传递的参数值加1然后返回原值，这就是后增操作符的定义。
现在让我们看看在你的代码中是怎么执行的。

```java
MutableInt x = new MutableInt();
x = postIncrement(x);
```

`postIncrement(x)`函数做了什么？x增加1，对，然后返回x加1之前的值，并且把该返回值赋给x。所以X被赋值的顺序是0，1，0。如果我们重写上面的代码会理解得更清楚。

```java
MutableInt x = new MutableInt();    // x is 0.
MutableInt temp = postIncrement(x); // Now x is 1, and temp is 0.
x = temp;                           // Now x is 0 again.
```

你定势地认为，将左边的X换个名称为y，"你能看到x先自增1，然后赋值给了y"，这一时也让我有点困惑。bi那个不是x被赋值给y，而是x（自增）之前的值被赋给y.事实上，把左边x变量名改成y结果也没什么不同。

```java
MutableInt x = new MutableInt();    // x is 0.
MutableInt y = new MutableInt();    // y is 0.
MutableInt temp = postIncrement(x); // Now x is 1, and temp is 0.
y = temp;                           // y is still 0.
```

可见，x=x++并没有改变x的值，只是让x依次被赋值为x0,x0+1,x0。

更新：你可能会怀疑在自增过程中x并没有被赋值为1，下面通过一个demo来解释确实x存在赋值为1的过程。
demo中调用了x=x++；同时另外开了一个单独的线程不断输出x的值.

```java
public class Main {
    public static volatile int x = 0;

    public static void main(String[] args) {
        LoopingThread t = new LoopingThread();
        System.out.println("Starting background thread...");
        t.start();

        while (true) {
            x = x++;
        }
    }
}

class LoopingThread extends Thread {
    public @Override void run() {
        while (true) {
            System.out.println(Main.x);
        }
    }
}
```

下面是程序的部分输出，0和1不规则的出现。

```
Starting background thread...
0
0
1
1
0
0
0
0
0
0
0
0
0
0
1
0
1
```


stackoverflow链接：
[http://stackoverflow.com/questions/3831341/why-does-this-go-into-an-infinite-loop](http://stackoverflow.com/questions/3831341/why-does-this-go-into-an-infinite-loop)
