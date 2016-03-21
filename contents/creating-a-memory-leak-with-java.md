# 如何使用Java创建一个内存泄漏的程序

## 问题：
我在一个面试的过程中被问到如何使用Java创建一个内存泄漏的程序。毫无疑问地说，我当时哑口无言，根本不知道如何开始编码。

## 解答
在Java下有一个很好的方法来创建内存泄漏程序--通过使得对象不可访问但任然存储在内存中。

1. 应用程序创建一个长期运行的线程A 或者 使用一个线程池来加快泄漏的速度。

2. 线程A使用ClassLoader（用户可以自定义）加载一个类 B。

3. 在类B申请一块很大的连续内存（例如：new byte[1000000]），
并使用一个静态成员变量保存该空间的一个强引用，之后在一个ThreadLocal对象中存储类B对象的引用。
虽然泄漏这个类的一个实例就足够了，但是也可以通过申请多个实例的方法来加快内存泄漏的速度。

4. 线程A清理所有指向自定义类或者通过ClassLoadeer加载的引用。

5. 重复上述步骤

上述方式可以达到内存泄漏的目的，因为 ThreadLocal 存储了一个指向类B对象的引用，
而该对象又保存了一个指向其类的引用，这个类又保存了一个指向其ClassLoader的引用，
而ClassLoader又保存了一个通过它加载的所有类的引用。
这种方法在许多的JVM的实现中表现更糟糕，因为Classes和ClassLoader被直接存储在老年代（permgen）并且永远都不会被GC处理。

******************************下方为个人理解************************************

通过一个简单的图来描述上述关系：<br>
ThreadLocal.obj ---> B.obj ---> B.class <--> ClassLoader.obj<br>
注：上图的\*.obj表示\*类的一个实例对象，B.class表示类B的Class对象

******************************上方为个人理解************************************

这个模式的一个变形：如果频繁的重新部署那些可能使用ThreadLocals的应用，应用容器（例如Tomcat）就会像筛子一样泄漏内存。
因为应用容器使用上述所说的线程，每次重新部署应用时，应用容器都会使用一个新的ClassLoader。

具体代码可以参考：https://gist.github.com/dpryden/b2bb29ee2d146901b4ae

参考：http://frankkieviet.blogspot.com/2006/10/classloader-leaks-dreaded-permgen-space.html

stackoverflow原址：http://stackoverflow.com/questions/6470651/creating-a-memory-leak-with-java
