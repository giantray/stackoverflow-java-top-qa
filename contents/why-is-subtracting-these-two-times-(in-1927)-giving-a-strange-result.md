## 为什么相减这两个时间（1927年）会得到奇怪的结果？

### 问题
如果运行下面的代码，我想要做的是解析两个相差1秒的日期字符串：
```java
public static void main(String[] args) throws ParseException {
    SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");  
    String str3 = "1927-12-31 23:54:07";  
    String str4 = "1927-12-31 23:54:08";  
    Date sDt3 = sf.parse(str3);  
    Date sDt4 = sf.parse(str4);  
    long ld3 = sDt3.getTime() /1000;  
    long ld4 = sDt4.getTime() /1000;
    System.out.println(ld4-ld3);
}
```
其结果是
```
353
```
为什么`ld4-ld3` 结果不是`1`（因为我期望得到1秒的时间差）而是`353`？

如果我把两个日期都换成1秒后：
```java
String str3 = "1927-12-31 23:54:08";  
String str4 = "1927-12-31 23:54:09"; 
```
这样`ld4-ld3`就为`1`。

---
java 版本
```
java version "1.6.0_22"
Java(TM) SE Runtime Environment (build 1.6.0_22-b04)
Dynamic Code Evolution Client VM (build 0.2-b02-internal, 19.0-b04-internal, mixed mode)
```
时区（`TimeZone.getDefault()`）：
```
sun.util.calendar.ZoneInfo[id="Asia/Shanghai",
offset=28800000,dstSavings=0,
useDaylight=false,
transitions=19,
lastRule=null]

Locale(Locale.getDefault()): zh_CN
```
## 回答
这是12月31号上海时区切换的错误。

[这个页面](http://www.timeanddate.com/time/change/china/shanghai?year=1927)上有关于上海1927的详细说明。因为1927年末尾的午夜，时间回滚了5分52秒，所以"1927-12-31 23:54:08" 实际上发生了两次，并且很明显java解析立即得到了可能的后面一个，而忽略了其差别。

这在经常弄混且丰富的世界时区中还是一个插曲。

如果重新编译[TZDB](https://github.com/nodatime/nodatime/blob/master/src/NodaTime.Demo/StackOverflowExamples.cs#L68)的2013a版本，之前的问题就不再能解释类型行为。在2013a 的版本中，结果将是358秒，切换时间由23:54:03 变为23:54:08。

我能注意到这个都是因为我在[unit tests](https://github.com/nodatime/nodatime/blob/master/src/NodaTime.Demo/StackOverflowExamples.cs#L68)收集像Noda Time 这样的问题

这个测试现在已经被更新，但是也表明没有历史数据是安全的。

在TZDB 2014f中, 这个时间切换移动到了1900-12-31，并且也被修改成唯一的343秒变化(如果你知道我在说啥的话，`t` 和 `t+1` 之间是343 秒)。

**更新** 为了回答[ Ken Kin's question](http://stackoverflow.com/questions/6841333/why-is-subtracting-these-two-times-in-1927-giving-a-strange-result/6841479#comment22684267_6841479)的问题，看起来像是，对于任何在1900 UTC 之前的时间，java 在他们的标准时间中进行了简单的时区实现。
```java
import java.util.TimeZone;

public class Test {
    public static void main(String[] args) throws Exception {
        long startOf1900Utc = -2208988800000L;
        for (String id : TimeZone.getAvailableIDs()) {
            TimeZone zone = TimeZone.getTimeZone(id);
            if (zone.getRawOffset() != zone.getOffset(startOf1900Utc - 1)) {
                System.out.println(id);
            }
        }
    }
}
```
上面的代码在我的windows 机器上没有任何输出，所以对于任何相对标准时间在1900年前且有偏移的时间，都会有时间过渡。TZDB 自己有数据回退到更早之前，并且不依赖任何修正过的标准时间（就是`getRawOffset`假设的有效的概念），所以其他库不再需要介绍这个切换。

stackoverflow[链接](http://stackoverflow.com/questions/6841333/why-is-subtracting-these-two-times-in-1927-giving-a-strange-result)