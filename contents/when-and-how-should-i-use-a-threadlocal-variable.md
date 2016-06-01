## 该什么时候使用 ThreadLocal变量，它是如何工作的？
### 回答1
一种可能的（也是常见的）使用情形是你不想通过同步方式（synchronized）访问非线程安全的对象（说的就是SimpleDateFormat）,而是想给每个线程一个对象实例的时候。
例如
````java
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
````
### 回答2
因为ThreadLocal是一个既定线程内部的数据引用，你可能在使用线程池的应用服务器上因此引起类加载时候的内存泄漏。你需要使用remove()方法很小心地清理TheadLocal中get()或者set()的变量。
如果程序执行完毕没有清理的话，它持有的任何对类的引用将作为部署的Web应用程序的一部分仍保持在永久堆，永远无法得到回收。重新部署/取消部署也无法清理对应用程序类的引用，因为线程不是被你的应用程序所拥有的。
每次成功部署都会创建一个永远不会被垃圾回收类的实例。

最后将会遇到内存不足的异常-java.lang.java.lang.OutOfMemoryError: PermGen space -XX:MaxPermSize，在google了很多答案之后你可能只是增加了-XX:MaxPermSize，而不是修复这个bug。
倘若你的确遇到这种问题，可以通过[Eclipse's Memory Analyzer](http://www.eclipse.org/mat/)或根据[Frank Kieviet's guide](https://blogs.oracle.com/fkieviet/entry/classloader_leaks_the_dreaded_java) 和 [followup](https://blogs.oracle.com/fkieviet/entry/how_to_fix_the_dreaded)来判断哪些线程和类保留了那些引用。

更新：又发现了[Alex Vasseur's blog entry](http://avasseur.blogspot.jp/2003/11/threadlocal-and-memory-leaks.html)，它帮助我查清楚了一些ThreadLocal的问题。

stackoverflow链接：http://stackoverflow.com/questions/817856/when-and-how-should-i-use-a-threadlocal-variable
