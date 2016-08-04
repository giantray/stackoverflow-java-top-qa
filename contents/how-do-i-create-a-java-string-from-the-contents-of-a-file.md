##如何从文件里读取字符串

###从文件里读取所有文本：

代码：
```java
static String readFile(String path, Charset encoding) 
  throws IOException 
{
  byte[] encoded = Files.readAllBytes(Paths.get(path));
  return new String(encoded, encoding);
}

```

###一行一行读入文本：

Java 7 提供了一个方便的方法可以直接将文件中的文本一行一行读入，存放在一个List容器里。
```JAVA
List<String> lines = Files.readAllLines(Paths.get(path), encoding);
```

###内存使用率

第一个方法，一次读取所有文本的方法，占用内存较多，因为它一次性保留了文件的所有原始信息，包括换行符之类的“无用”字符。

第二个方法，按行读入，比起一次性全部读入，要消耗更少的内存。因为它每次只将一行的文件信息放在缓存中。然而，如果文本文件很大，这种方法依然会占用很多内存。

如果你的程序需要处理很大的文本文件，在设计的时候就要考虑，分配一块固定的缓存，每次从流中读入文件的一部分放入缓存，处理，然后清空缓存，把下一部分读入缓存，直到处理完所有的数据。

这里的“很大”是相对于计算机性能的。一般来说，几十个G的文件应当算是大文件。

###字符编码

还有一件事需要注意，就是字符编码。不同的平台有自己的默认编码，所以有时候你的程序需要指定编码，来保持平台无关/跨平台。

```StandardCharsets``` 类定义了常用的编码类型，你可以用如下方法调用：

```java
String content = readFile("test.txt", StandardCharsets.UTF_8);
```

可以通过```Charset```类来获得平台默认的字符编码。

```java
String content = readFile("test.txt", Charset.defaultCharset());
```

注： 这个答案与之前Java6版本时的答案完全不同。Java 7 新增的工具类极大的优化了字符处理，文件读取等功能。Java 6 常用的内存映射方法已不适合在Java 7 以后的版本使用。

### 原文链接
http://stackoverflow.com/questions/326390/how-do-i-create-a-java-string-from-the-contents-of-a-file
   


