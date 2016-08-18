## 如何创建泛型java数组

#### 问题

数组是不能通过泛型创建的，因为我们不能创建不可具体化的类型的数组。如下面的代码：

```java
public class GenSet<E> {
    private E a[];

    public GenSet() {
        a = new E[INITIAL_ARRAY_LENGTH]; //编译期就会报错：不能创建泛型数组
    }
}
```

#### 采纳答案

* 检查：强类型。`GenSet`明确知道数组中包含的类型是什么（例如通过构造器传入`Class<E>`，当方法中传入类型不是`E`将抛出异常）

```java
public class GenSet<E> {

    private E[] a;

    public GenSet(Class<E> c, int s) {
        // 使用原生的反射方法，在运行时知道其数组对象类型
        @SuppressWarnings("unchecked")
        final E[] a = (E[]) Array.newInstance(c, s);
        this.a = a;
    }

    E get(int i) {
        return a[i];
    }
    
    //...如果传入参数不为E类型，那么强制添加进数组将会抛出异常
    void add(E e) {...}
}
```

* 未检查：弱类型。数组内对象不会有任何类型检查，而是作为Object类型传入。

在这种情况下，你可以采取如下写法：

```java
public class GenSet<E> {

    private Object[] a;

    public GenSet(int s) {
        a = new Object[s];
    }

    E get(int i) {
        @SuppressWarnings("unchecked")
        final E e = (E) a[i];
        return e;
    }
}
```

上述代码在编译期能够通过，但因为泛型擦除的缘故，在程序执行过程中，数组的类型有且仅有`Object`类型存在，这个时候如果我们强制转化为`E`类型的话，在运行时会有`ClassCastException`抛出。所以，要确定好泛型的上界，将上边的代码重写一下：

```java
public class GenSet<E extends Foo> { // E has an upper bound of Foo

    private Foo[] a; // E 泛型在运行期会被擦除为Foo类型，所以这里使用Foo[]

    public GenSet(int s) {
        a = new Foo[s];
    }
    
    //...
}
```

#### StackOverflow地址：

[http://stackoverflow.com/questions/529085/how-to-create-a-generic-array-in-java](http://stackoverflow.com/questions/529085/how-to-create-a-generic-array-in-java)