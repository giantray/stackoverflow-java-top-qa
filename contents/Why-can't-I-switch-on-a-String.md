# Why can't I switch on a String

##问题描述
为什么不能用string类型进行switch判断？
是否作为一个功能将会被添加如java后续版本中？
有人能给我一篇文章，或者解释一下为什么不能这样做，说明java中switch语句的运行方式？

##回答
用string作为case来使用switch语句已经在java SE7中被实现了，距离这个问题被提出至少有16年了。一个明确的原因迟迟未提供，但是性能要求它必须这样做。

**Implementtation in JDK 7**
这个特性现在已经被实现，`javac`with a ["de-sugaring peocess"](http://blogs.oracle.com/darcy/entry/project_coin_string_switch_anatomy)。一个高级的，清楚的使用String常量的语法能在case声明中被使用，会在编译时扩展成为更复杂的代码模式，生成的代码使用jvm指令也一直存在。
switch将string用作case在编译时翻译成两个switch。第一次每一个string映射到一个独一无二的整数，它的位置在原始的switch上。这是通过在case上的hashcode进行switch选择，相应的案例是一个if语句，测试字符串是否相等。如果有哈希碰撞，测试就像`if-else-if`。
第二次switch反映在最初的源代码中，用相应位置上的整数代替响应的string标签case。这两步过程更容易保留原始switch的流程控制。

**Switchs in the JVM**
在switch的更多深层技术上，可以参考JVM规范，[compliation of switch statements](http://docs.oracle.com/javase/specs/jvms/se7/html/jvms-3.html#jvms-3.10)有响应的描述。简单概括说，根据使用的常量的多少，switch有两种不同的JVM指令来执行，每种情况下都取决于使用整数常量来有效的执行。

如果常量很多，case会在一个指令表被用作索引（减去最小值后）——`tablewitch`指令
如果常量相对较少，那么可用二分查找来找到正确的case--`lookupswitch`指令

在`de-sugaring`中，一个switch（String）的对象，两种指令都会被用到。`lookupswitch`指令适用于第一次计算哈希值找到初始位置，由此产生的顺序表很自然的用`tableswitch`指令

两种指令在编译时都需要将整数常量赋值给每个case。在运行时，虽然`tableswitch`O（1）的性能通常要好于`lookupswitch`O(log(n))的性能，但是前者需要一定的分析来决定是否有足够的case来满足时间空间的平衡。Bill Venners的文章[a great article](http://www.artima.com/underthehood/flowP.html)在细节上讲述的更多，以及涉及到从底层的出发，来看其他java流程控制指令。

**Before JDK 7**
在JDK之前，枚举近似的用String作为switch的case，这使用了静态方法`valueOf`，编译器生成了一个枚举类型。例子如下：
```
Pill p = Pill.valueOf(str);
switch(p) {
    case RED:pop();break;
    case BLUE:push();break;
}
```

[阅读原文：Why can`t I switch on a String](http://stackoverflow.com/questions/338206/why-cant-i-switch-on-a-string)





