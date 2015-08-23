## HashMap和Hashtable的区别
### 问题
在Java中`HashMap`和`Hashtable`的区别？
哪一个对于非线程应用程序更有效？

### 回答
1. `Hashtable`是同步的，而`HashMap`不是。这让`HashMap`对于非线程应用程序来说更加好一点，对于非同步对象通常表现得比同步的要好。
2. `Hashtable`不允许有空的键或值。`HashMap`允许空的键和任何数值的空值。
3. HashMap的一个子类是[LinkedHashMap](http://docs.oracle.com/javase/7/docs/api/java/util/LinkedHashMap.html),如果你想预知迭代的顺序（插入的时候就自动排序了），你能轻易的从`LinkedHashMap`转化成`HashMap`。对于`Hashtable`就不那么容易。
如果同步对于你不是那么重要的话，我会建议用`HashMap`.如果同步很重要的话，你可以看看[ConcurrentHashMap](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ConcurrentHashMap.html)

### stackoverflow链接：
http://stackoverflow.com/questions/40471/differences-between-hashmap-and-hashtable

### 相关推荐：
importnew:[HashMap和Hashtable的区别](http://www.importnew.com/7010.html)