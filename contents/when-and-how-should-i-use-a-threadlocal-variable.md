##应该在什么时候和如何使用一个ThreadLocal变量

###回答

一个可能的（经常）使用的情况是当你有一些对象不是线程安全的，但你想要避免同步访问该对象（说的就是你，SimpleDateFormat）。
这时，给每个线程它自己的对象的实例。

例子:


```java
public class Foo
{
    // SimpleDateFormat is not thread-safe, so give one to each thread
    private static final ThreadLocal<SimpleDateFormat> formatter = new ThreadLocal<SimpleDateFormat>(){
        @Override
        protected SimpleDateFormat initialValue()
        {
            return new SimpleDateFormat("yyyyMMdd HHmm");
        }
    };

    public String formatIt(Date date)
    {
        return formatter.get().format(date);
    }
}
```




stackoverflow讨论原址
http://stackoverflow.com/questions/817856/when-and-how-should-i-use-a-threadlocal-variable
