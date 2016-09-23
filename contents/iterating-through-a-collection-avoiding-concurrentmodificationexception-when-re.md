## 遍历集合时移除元素，怎样避免ConcurrentModificationException异常抛出

#### 问题：

在遍历集合的过程中，不会总出现`ConcurrentModificationException`异常的抛出，但是在下面的代码块中：

```java
public static void main(String[] args) {
    Collection<Integer> l = new ArrayList<Integer>();

    for (int i=0; i < 10; ++i) {
        l.add(new Integer(4));
        l.add(new Integer(5));
        l.add(new Integer(6));
    }

    //遍历的过程中移除部分集合元素
    for (Integer i : l) {
        if (i.intValue() == 5) {
            l.remove(i);
        }
    }

    System.out.println(l);
}
```

运行之后，结果显而易见，总是会抛出异常：

```java
Exception in thread "main" java.util.ConcurrentModificationException
```

所以，遍历集合时移除元素，怎样避免ConcurrentModificationException异常的产生？有什么好的解决办法？

#### 回答：

`Iterator.remove()`是线程安全的，所以你的代码可以这样写：

```java
List<String> list = new ArrayList<>();

for (Iterator<String> iterator = list.iterator(); iterator.hasNext();) {
    String string = iterator.next();
    if (string.isEmpty()) {
    
        // 从迭代器中移除集合元素，集合中相应的集合元素也会安全地被移除
        // 在这里，如果继续调用的是list.remove(string)，那么仍会抛出异常
        iterator.remove();
    }
}
```

在遍历集合时修改集合的结构或内容的情况中，`Iterator.remove()`是唯一线程安全的方法。

#### 问题原因：

fail-fast, 快速失败机制，是java集合类的一种错误检查机制。当有多个线程同时对集合进行遍历以及内容或者结构的修改时，就有可能产生fail-fast机制。这意味着，当它们发现容器在迭代的过程中被修改时，就会抛出一个ConcurrentModificationException异常。

迭代器的快速失败行为无法得到保证，它不能保证一定会出现该错误，但是快速失败操作会尽最大努力抛出ConcurrentModificationException异常，这个异常仅用于检测bug。这种迭代器并不是完备的处理机制，而只是作为并发问题的一个预警指示器。


#### 拓展阅读：

[fail-fast机制的原理解析](https://github.com/AcceptedBoy/backstage-vacation-plan/blob/master/chapter1/concurrency/fail-fast.md)

#### StackOverFlow地址：

[http://stackoverflow.com/questions/223918/iterating-through-a-collection-avoiding-concurrentmodificationexception-when-re](http://stackoverflow.com/questions/223918/iterating-through-a-collection-avoiding-concurrentmodificationexception-when-re)