##将InputStream转换为String

###使用Apache库
不重复造轮子。最靠谱的方法，还是用Apache commons IOUtils 
这样简单几行代码就搞定了
```java
StringWriter writer = new StringWriter();
IOUtils.copy(inputStream, writer, encoding);
String theString = writer.toString();
```
或者
String theString = IOUtils.toString(inputStream, encoding)//这个方法其实封装了上面的方法，减少了一个参数

###使用原生库
如果不想引入Apache库，也可以这样做
```java
static String convertStreamToString(java.io.InputStream is) {
  java.util.Scanner s = new java.util.Scanner(is).useDelimiter("\\A"); 
  return s.hasNext() ? s.next() : "";
}
```

stackoverflow讨论地址
http://stackoverflow.com/questions/309424/read-convert-an-inputstream-to-a-string