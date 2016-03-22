##能否在一个构造器中调用另一个构造器

###问题
能否在一个构造器中调用另一个构造器（在同一个类中，不是子类）？如果可以，怎么做？
调用另一个构造器的最好方法是什么（如果有几种方法可以选择的话）？


###回答
可以这样做：
```java
public class Foo
{
    private int x;

    public Foo()
    {
        this(1);
    }

    public Foo(int x)
    {
        this.x = x;
    }
}
```
如果你想调用一个特定的父类构造器，而不是本类的构造器，应该使用super，而不是this.
请注意，在构造器中，你只能调用一次其他的构造器。并且调用其他构造器的语句，必须是这个构造器的第一个语句。

stackoverflow原址：
http://stackoverflow.com/questions/285177/how-do-i-call-one-constructor-from-another-in-java
