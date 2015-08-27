## HashMap和Hashtable的区别
### 问题
在Java中`HashMap`和`Hashtable`的区别？
哪一个对于多线程应用程序更好？

### 回答
1. `Hashtable`是同步的，加了`synchronized`锁，而`HashMap`不是。没有加`synchronized`锁的对象，性能通常比加了`synchronized`锁的对象要更好一些，因此，如果是非多线程程序，不需要考虑锁、同步等问题，那么使用`HashMap`更好。
2. `Hashtable`不允许有空的键或值。`HashMap`允许空键和空值。
3. HashMap有一个子类[LinkedHashMap](http://docs.oracle.com/javase/7/docs/api/java/util/LinkedHashMap.html),对这个类对象进行迭代时，它的顺序是有序的（按插入顺序排序）。如有需要，你也能轻易的从`LinkedHashMap`转化成`HashMap`。`Hashtable`就没那么简单了，

总之，如果你无需关心同步（synchronized）问题，我会建议用`HashMap`。反之，你可以考虑使用[ConcurrentHashMap](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ConcurrentHashMap.html)

### stackoverflow链接：
http://stackoverflow.com/questions/40471/differences-between-hashmap-and-hashtable

### 相关推荐：
importnew:[HashMap和Hashtable的区别](http://www.importnew.com/7010.html)