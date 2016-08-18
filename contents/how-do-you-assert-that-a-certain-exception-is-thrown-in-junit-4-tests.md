## JUnit4如何断言确定异常的抛出

#### 问题

在JUnit4单元测试中，我要怎样做才能测试出有特定的异常抛出？我能想到的就只有下面的方法：

```java
@Test
public void testFooThrowsIndexOutOfBoundsException() {
    boolean thrown = false;

    try {
        foo.doStuff();
    } catch (IndexOutOfBoundsException e) {
        thrown = true;
    }

    assertTrue(thrown);
}
```

#### 回答1

在JUnit4后支持下面的写法：
```java
@Test(expected=IndexOutOfBoundsException.class)
public void testIndexOutOfBoundsException() {
    ArrayList emptyList = new ArrayList();
    Object o = emptyList.get(0);
}
```
（译者：在`@Test`注解内提供了`expected`属性，你可以用它来指定一个`Throwble`类型，如果方法调用中抛出了这个异常，那么这条测试用例就相当于通过了）

#### 回答2

如果你使用的是JUnit4.7，你可以使用如下的期望异常规则来验证异常信息：

```java
public class FooTest {
    @Rule
    public final ExpectedException exception = ExpectedException.none();

    @Test
    public void doStuffThrowsIndexOutOfBoundsException() {
        Foo foo = new Foo();

        exception.expect(IndexOutOfBoundsException.class);
        foo.doStuff();
    }
}
```

这种方式比`@Test(expected=IndexOutOfBoundsException.class)`要更好，如果是在调用`foo.doStuff()`方法之前就已经抛出异常的话，测试结果就不是我们想要的了。
（译者：同时，`ExpectedException`还能够验证异常信息，如`exception.expectMessage("there is an exception!");`

#### 拓展阅读

1. [JUnit：使用ExpectedException进行异常测试](http://www.tuicool.com/articles/ANviIz)
2. [JUnit4 用法详解](http://www.blogjava.net/jnbzwm/archive/2010/12/15/340801.html)

#### StackOverflow地址：

[http://stackoverflow.com/questions/156503/how-do-you-assert-that-a-certain-exception-is-thrown-in-junit-4-tests](http://stackoverflow.com/questions/156503/how-do-you-assert-that-a-certain-exception-is-thrown-in-junit-4-tests)