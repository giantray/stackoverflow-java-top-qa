# 为什么不能用string类型进行switch判断

## 问题描述
为什么不能用string类型进行switch判断？
在java的后续版本中，是否会增加这个新特性？
有人能给我一篇文章，解释一下为什么不能这样做，或者进一步说明java中switch语句的运行方式？

## 回答
在switch语句中用string作为case，这个特性已经在java SE7 中被实现了，距离 [这个'bug'](http://bugs.sun.com/bugdatabase/view_bug.do?bug_id=1223179) 被提出至少也有16年了。为何迟迟不提供这个特性，原因不明。但可以推测，可能跟性能有关。

**Implementtation in JDK 7**

在JDK7中，这个特性已经实现了。在编译阶段，以string作为case值的代码，会按照特定的模式，被转换为更加复杂的代码。最终的执行代码将是一些使用了JVM指令的代码。

究竟是如何转换的呢？我们直接看看源码及编译后的代码。源代码：
```
public class StringInSwitchCase { 
      public static void main(String[] args) { 
            String mode = args[0]; 
            switch (mode) { 
                  case "ACTIVE": 
                        System.out.println("Application is running on Active mode"); 
                        break; 
                  case "PASSIVE":
                        System.out.println("Application is running on Passive mode"); 
                         break; 
                  case "SAFE": 
                          System.out.println("Application is running on Safe mode"); 
          } 
      } 
}
```
编译后再反编译的代码：
```
import java.io.PrintStream; 

public class StringInSwitchCase{ 
      public StringInSwitchCase() { } 

      public static void main(string args[]) { 
             String mode = args[0]; 
            String s; switch ((s = mode).hashCode()) { 
                  default: break; 
                  case -74056953: 
                        if (s.equals("PASSIVE")) { 
                                    System.out.println("Application is running on Passive mode"); 
                         } 
                        break; 
                  case 2537357: 
                        if (s.equals("SAFE")) { 
                              System.out.println("Application is running on Safe mode"); 
                         } 
                        break; 
                  case 1925346054: 
                        if (s.equals("ACTIVE")) { 
                              System.out.println("Application is running on Active mode"); 
                         } 
                        break; 
               } 
          } 
}
```

包含case string的 switch 语句，在编译时会转为为嵌套代码（switch+if）。第一个switch将 case 中的string转为唯一的integer值。这个integer值就是原先string的hashcode值。在case的逻辑中，会加入if语句，这个if语句用于进一步检查string值是否跟原先的case string匹配。这样可以防止hash碰撞，确保代码的健壮。这本质上是一种语法糖，既支持了string作为case值这一特性，又能确保逻辑正确性。

**Switchs in the JVM**

switch的更多深层技术实现，可以参考JVM规范，[compliation of switch statements](http://docs.oracle.com/javase/specs/jvms/se7/html/jvms-3.html#jvms-3.10)。简单概括说，根据使用的常量的多寡，switch会对应到两种不同的JVM指令。JVM指令有所不同，归根结底都是为了代码的效率。

如果常量很多，会将case的int值去掉最低位后作为索引，放到一个指针表中——也就是所谓的`tablewitch`指令

如果常量相对较少，那么可用二分查找来找到正确的case--也就是所谓的`lookupswitch`指令

这两种指令，都要求在编译时确保case的对应值是integer常量。在运行时，虽然`tableswitch`O（1）的性能通常要好于`lookupswitch`O(log(n))的性能。但是前者需要更多的空间开销，因此需要兼顾空间及时间综合考虑性价比。Bill Venners的文章[a great article](http://www.artima.com/underthehood/flowP.html)有更多深入的分析。

**Before JDK 7**

在JDK之前，可以用枚举来实现类似的需求。它和在case中使用string有异曲同工之妙。例如如下：
```
Pill p = Pill.valueOf(str);
switch(p) {
    case RED:pop();break;
    case BLUE:push();break;
}
```

[stackoverflow原链接：Why can`t I switch on a String](http://stackoverflow.com/questions/338206/why-cant-i-switch-on-a-string)

[可参考中文文章《Java中字符串switch的实现细节》](http://www.deepinmind.com/java/2014/05/08/how-string-in-switch-works-in-java-7.html)





