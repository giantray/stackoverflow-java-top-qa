## wait()和sleep()的区别

### 问题：
在线程里 `wait()` 和 `sleep()` 的区别？

我的理解是执行 `wait()` 语句后，该线程仍是运行态，并且会占用CPU，但是执行 `sleep()`后，该线程则不会占用CPU，对吗？

为什么需要 `sleep()` 和 `wait()` 两条语句：他们底层是如何实现的？

### 回答：
线程 在[`wait`](http://docs.oracle.com/javase/6/docs/api/java/lang/Object.html#wait%28%29) 后，可以被另一个拥有相同 `synchronized ` 对象的线程，通过调用[ `notify` ](http://docs.oracle.com/javase/6/docs/api/java/lang/Object.html#notify%28%29)唤醒，而 `sleep` 不行。`wait` 和 `notify` 能正常执行的条件是（否则会抛异常）：多个线程的代码，都包在`synchronized `块中，并且 `synchronized` 锁的对象需要是同一个。如下所示：
```
Object mon = ...;
synchronized (mon) {
    mon.wait();
} 
```
上面这个线程调用了 `wait`后，会进入等待状态。这时另外一个线程可以这样做：
```
synchronized (mon) { mon.notify(); }
```
可以看到，`synchronized`锁对象，都是mon。因此，当第二个线程调用了 `notify()` 方法，第一个线程就会唤醒（假设有且仅有一个线程是被包在 `synchronized (mon)` 中且处于等待状态)。

如果有多个线程在等待（且`synchronized` 锁对象是同一个，如上例中的mon），则可以调用[ `notifyAll` ](http://docs.oracle.com/javase/6/docs/api/java/lang/Object.html#notifyAll%28%29)来唤醒。但是，只有其中一个线程能抢到锁并继续执行（因为  `wait` 的线程都是在 `synchronized` 块内，需要争夺 `synchronized` 锁）。其他的线程会被锁住，直到他们依次获得锁。
 
再补充几点：

-  `wait` 方法由 `Object` 对象调用（例如：你可以让 `synchronized` 锁对象调用 `wait` ,如上面例子的mon.wait()）,而 `sleep` 则由线程调用。

-  `wait` 之后，可能会伪唤醒（`spurious wakeups`）（正在waiting的线程,无故就被唤醒了，如遇到interrupted, timing out等情况）。因此，你需要多设置一些检查，如果不满足实际的运行条件，则继续等待，如下：
```
synchronized {
    while (!condition) { mon.wait(); }
}
```

- 当线程调用 `sleep` 时，并没有释放对象锁，而 `wait` 则释放了对象锁：
```
synchronized(LOCK) {
    Thread.sleep(1000); // LOCK is held
}
synchronized(LOCK) {
    LOCK.wait(); // LOCK is not held
}
```

最后，再小结一下：
- ` sleep() `:“我已经完成了一个时间片，**在n微秒**前，请不要再给我一个时间片”。这时操作系统不会让这个线程做任何事情，直到sleep时间结束。
- ` wait() `:"我已经完成了一个时间片，**在其他线程调用`  notify() `前**，请不要再给我一个时间片）。这时操作系统不会安排这个线程继续运行，直到有人调用了` notify() `



**stackoverflow链接：**
http://stackoverflow.com/questions/1036754/difference-between-wait-and-sleep

### 相关问题及链接：

1. [Java: notify() vs. notifyAll() all over again](http://stackoverflow.com/questions/37026/java-notify-vs-notifyall-all-over-again)
2. [线程通信](http://ifeve.com/thread-signaling/)
3. [最简实例说明wait、notify、notifyAll的使用方法](http://longdick.iteye.com/blog/453615)