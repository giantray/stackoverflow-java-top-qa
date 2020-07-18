##将数组转换为List

###问题
假设有数组
```java
Element[] array = {new Element(1),new Element(2),new Element(3)};
```
如何将其转换为ArrayList`<Element>` arraylist = ？？？

### 回答1 ###

```java
new ArrayList<Element>(Arrays.asList(array))
```

###回答2

`Arrays.asList(array)`或者`Arrays.asList(new Element(1),new Element(2),new Element(3))`

不过，这样做有些坑要注意：

1. 这样做生成的`list`，是定长的。也就是说，如果你对它做`add`或者`remove`，都会抛`UnsupportedOperationException`。
2. 如果修改数组的值，`list`中的对应值也会改变！

**`Arrays.asList()`返回的是`Arrays`内部静态类，而不是`Java.util.ArrayList`的类。这个`java.util.Arrays.ArrayList`有`set()`,`get()`,`contains()`方法，但是没有任何`add()`方法，所以它是固定大小的**


如果希望避免这两个坑，请改用这个方式
```java
Collections.addAll(arraylist, array);
```

stackoverflow原址：
http://stackoverflow.com/questions/157944/how-to-create-arraylist-arraylistt-from-array-t
