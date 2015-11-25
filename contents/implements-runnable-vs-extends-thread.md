##实现Runnable接口  VS. 继承Thread类  ？

在Java中，并发执行任务一般有两种方式：
（1）实现Runnable接口
（2）继承Thread类

一般而言，推荐使用方式（1）,主要是由于大多数情况下，人们并不会特别去关注线程的行为，也不会去改写Thread已有的行为或方法，仅仅是期望执行任务而已。
因此，使用接口的方式能避免引入一些并不需要的东西，同时也不会影响继承其他类，并使程序更加灵活。


###额外的tips：
（1）Runnable与Thread不是对等的概念
在Thinking in Java中，作者吐槽过Runnable的命名，称其叫做Task更为合理。
在Java中，Runnable只是一段用于描述任务的代码段而已，是静态的概念，需要通过线程来执行。而Thread更像是一个活体，自身就具有很多行为，能够用来执行任务。

（2）仅仅当你确实想要重写（override）一些已有行为时，才使用继承，否则请使用接口。

（3）在Java 5之前，创建了Thread却没调用其start()方法，可能导致内存泄露。


stackoverflow链接：
http://stackoverflow.com/questions/541487/implements-runnable-vs-extends-thread

