如何创建一个java泛型数组
问题：
由于Java泛型的实现机制，你不能这样写代码：
pulic class GenSet<E>{
	private E a[];
	public GetSet(){
		a=new E[INITIAL_ARRAY_LENGTH]; //error:generic array creation
	}
}
在保证类型安全的情况下，我该如何实现创建一个Java泛型数组？
我在一个Java论坛上看到是这样解决的：
import java.lang.reflect.Array;

class Stack<T> {
    public Stack(Class<T> clazz, int capacity) {
        array = (T[])Array.newInstance(clazz, capacity);
    }

    private final T[] array;
}
但我不懂发生了什么。有人能帮我吗？

回答：
在回答之前，我得问你一个问题，你的GetSet是"checked"还是"unchecked"？
什么意思呢？
Checked的话，是强类型。GetSet明确地知道包含了什么类型的对象。
比如，当要传递不是E类型的实参时，它的构造器会被Class<E>引数明确地调用，方法会抛出一个异常。参阅Collections.checkedCollection。
在这种情况下，你应该这样写：
public class GenSet<E> {

    private E[] a;

    public GenSet(Class<E> c, int s) {
        // Use Array native method to create array
        // of a type only known at run time
        @SuppressWarnings("unchecked")
        final E[] a = (E[]) Array.newInstance(c, s);
        this.a = a;
    }

    E get(int i) {
        return a[i];
    }
}
Unchecked的话：弱类型。实际上要传递任何对象的实参时
是没有类型检查的。
在这种情况下，你应当这样写：
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
注意数组里的元素类型应当是可擦除的形参。
public class GenSet<E extends Foo> { // E has an upper bound of Foo

    private Foo[] a; // E erases to Foo, so use Foo[]

    public GenSet(int s) {
        a = new Foo[s];
    }

    ...
}
所有的这些结果来自Java一个有名，存心，不足的泛型：它通过使用
erasure实现，所以“泛型”类在运行时创建是不知道它的实参类型的，
所以不能提供类型安全，除非某些明确的机制（比如类型检查）已经实现了。
StackOverflow地址：
http://stackoverflow.com/questions/529085/how-to-create-a-generic-array-in-java