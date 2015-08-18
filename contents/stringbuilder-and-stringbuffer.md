##StringBuilder和StringBuffer有哪些区别呢

###最主要的区别，StringBuffer的实现用了synchronized（锁），而StringBuilder没有。

因此，StringBuilder会比StringBuffer快。

如果你
- 非常非常追求性能（其实这两个都不慢，比直接操作String，要快非常多了）
- 不需要考虑线程安全问题,
- JRE是1.5+

可以用StringBuilder,反之，请用StringBuffer。

性能测试例子:

如下这个例子，使用StringBuffer，耗时2241ms,而StringBuilder是753ms
```java
public class Main {
    public static void main(String[] args) {
        int N = 77777777;
        long t;

        {
            StringBuffer sb = new StringBuffer();
            t = System.currentTimeMillis();
            for (int i = N; i --> 0 ;) {
                sb.append("");
            }
            System.out.println(System.currentTimeMillis() - t);
        }

        {
            StringBuilder sb = new StringBuilder();
            t = System.currentTimeMillis();
            for (int i = N; i --> 0 ;) {
                sb.append("");
            }
            System.out.println(System.currentTimeMillis() - t);
        }
    }
}
```


stackoverflow讨论原址
http://stackoverflow.com/questions/355089/stringbuilder-and-stringbuffer
