##为什么这两个时间（1927年）相减会得到一个奇怪的结果？

###问题描述
如果我运行如下的程序，将两个相距一秒的日期解析成字符串并比较他们。
```
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

输出结果为:
```
353
```

为什么`ld4-ld3`不是`1`（正如我所期望的那样），而是`353`?

如果我把时间改变为之后的一秒:
```
String str3 = "1927-12-31 23:54:08";  
String str4 = "1927-12-31 23:54:09"; 
```

这时，`ld4-ld3`的结果为`1`.

java版本：
```
java version "1.6.0_22"
Java(TM) SE Runtime Environment (build 1.6.0_22-b04)
Dynamic Code Evolution Client VM (build 0.2-b02-internal, 19.0-b04-internal, mixed mode)
```

时区：
```
sun.util.calendar.ZoneInfo[id="Asia/Shanghai",
offset=28800000,dstSavings=0,
useDaylight=false,
transitions=19,
lastRule=null]

Locale(Locale.getDefault()): zh_CN
```

###问题回答
这是因为1927年11月31日上海的时区改变了。
观看[此页](http://www.timeanddate.com/time/change/china/shanghai?year=1927)获得更多关于上海1927年的细节。
这个问题主要是由于在1927年12月31日的午夜，时钟回调了5分钟零52秒。
所以"1927-12-31 23:54:08"这个时间实际上发生了两次，看上去java将这个时间解析为之后的那个瞬间。
因此出现了这种差别。

这只是美好但奇怪的世界时区中的一个插曲。

stackoverflow链接：[Why is subtracting these two times (in 1927) giving a strange result?](http://stackoverflow.com/questions/6841333/why-is-subtracting-these-two-times-in-1927-giving-a-strange-result)
