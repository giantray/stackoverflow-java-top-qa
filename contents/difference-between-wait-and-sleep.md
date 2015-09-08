## wait()和sleep()的区别

### 问题：
在线程里`wait()`和`sleep()`的区别？
我的理解是一条`wait()`线程语句后，仍然在运行这个模块，并且占用CPU，但是一条`sleep()`语句并不会占用CPU，对吗？
为什么需要`sleep()`和`wait()`两条语句：他们在底层是怎么执行的？

### 回答：
[`wait`](http://docs.oracle.com/javase/6/docs/api/java/lang/Object.html#wait%28%29)可以被另一个进程唤醒通过调用[`notify`](http://docs.oracle.com/javase/6/docs/api/java/lang/Object.html#notify%28%29)而`sleep`不行。并且`wait`和`notify`必须在监控对象同步块内发生而`sleep`不行。
```
Object mon = ...;
synchronized (mon) {
    mon.wait();
} 
```
在此刻正在执行的线程`wait`后释放了监视器。另外一个线程可能在做
```
synchronized (mon) { mon.notify(); }
```
(同一个`mon`对象)第一个线程（假设它是唯一在监视器上等待的线程）将会唤醒。
如果有很多的线程在等待你可以调用[`notifyall`](http://docs.oracle.com/javase/6/docs/api/java/lang/Object.html#notifyAll%28%29)来唤醒。但是只有其中之一能抢夺到该对象的监视器并继续执行（`wait`是在`synchronized `块内）。其他的线程会被锁住直到他们获得监视器的锁。
另外就是你可以`wait`对象本身（例如：你可以`wait`一个对象的监视器）但是你只能`sleep`线程。
另外还有你可能会得到伪唤醒（例如：等待的线程没有任何操作就唤醒了）
如果你按照下面的做法，你就能一直处于等待的状态尽管出现一些原因使他唤醒了。
```
synchronized {
    while (!condition) { mon.wait(); }
}
```
(wait前会释放监视器，被唤醒后又要重新获取，这瞬间可能有其他线程刚好先获取到了监视器，从而导致状态发生了变化， 这时候用while循环来再判断一下条件（比如队列是否为空）来避免不必要或有问题的操作。)

**stackoverflow链接：**
http://stackoverflow.com/questions/1036754/difference-between-wait-and-sleep

### 相关问题及链接：

1. [Java: notify() vs. notifyAll() all over again](http://stackoverflow.com/questions/37026/java-notify-vs-notifyall-all-over-again)
2. [线程通信](http://ifeve.com/thread-signaling/)
