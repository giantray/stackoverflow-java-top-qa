## 用java怎么创建一个文件并向该文件写文本内容

### 问：在java里最简单的创建文件写文件的方法是什么

### 最佳答案:
创建一个文本文件（注意：如果该文件存在，则会覆盖该文件）
````java
PrintWriter writer = new PrintWriter("the-file-name.txt", "UTF-8");
writer.println("The first line");
writer.println("The second line");
writer.close();
````
创建一个二进制文件（同样会覆盖这文件）
````java
byte data[] = ...
FileOutputStream out = new FileOutputStream("the-file-name");
out.write(data);
out.close();
````

Java 7+ 用户可以用[`File`](http://docs.oracle.com/javase/7/docs/api/index.html?java/nio/file/Files.html)类来写文件
创建一个文本文件
````java
List<String> lines = Arrays.asList("The first line", "The second line");
Path file = Paths.get("the-file-name.txt");
Files.write(file, lines, Charset.forName("UTF-8"));
//Files.write(file, lines, Charset.forName("UTF-8"), StandardOpenOption.APPEND);
````
创建一个二进制文件
````java
byte data[] = ...
Path file = Paths.get("the-file-name");
Files.write(file, data);
//Files.write(file, data, StandardOpenOption.APPEND);
````

### 其他的答案（1）:
在Java 7+中
````java
try (Writer writer = new BufferedWriter(new OutputStreamWriter(
              new FileOutputStream("filename.txt"), "utf-8"))) {
   writer.write("something");
}
````
还有一些实用的方法如下：
* [`FileUtils.writeStringtoFile(..)`](https://commons.apache.org/proper/commons-io/apidocs/org/apache/commons/io/FileUtils.html#writeStringToFile%28java.io.File,%20java.lang.String,%20java.nio.charset.Charset%29) 来自于 commons-io 包
* [`Files.write(..)`](http://docs.guava-libraries.googlecode.com/git/javadoc/com/google/common/io/Files.html#write%28java.lang.CharSequence,%20java.io.File,%20java.nio.charset.Charset%29) 来自于 guava

Note also that you can use a FileWriter, but it uses the default encoding, 
which is often a bad idea - it's best to specify the encoding explicitly.
还要注意可以使用 `FileWriter`，但是它使用的是默认编码，这不是很好的方法，最好是明确指定编码


下面是来自于prior-to-java-7的原始方法
````java
Writer writer = null;

try {
    writer = new BufferedWriter(new OutputStreamWriter(
          new FileOutputStream("filename.txt"), "utf-8"));
    writer.write("Something");
} catch (IOException ex) {
  // report
} finally {
   try {writer.close();} catch (Exception ex) {/*ignore*/}
}
````
可以看[`Reading, Writing, and Creating Files`](http://docs.oracle.com/javase/tutorial/essential/io/file.html)(包含NIO2)

### 其他答案（2）：
````java
public class Program {
    public static void main(String[] args) {
        String text = "Hello world";
        BufferedWriter output = null;
        try {
            File file = new File("example.txt");
            output = new BufferedWriter(new FileWriter(file));
            output.write(text);
        } catch ( IOException e ) {
            e.printStackTrace();
        } finally {
            if ( output != null ) output.close();
        }
    }
}
````

### 其他答案（3）：
如果已经有想要写到文件中的内容，[`java.nio.file.Files`](https://docs.oracle.com/javase/7/docs/api/java/nio/file/Files.html) 作为 Java 7 附加部分的native I/O，提供了简单高效的方法来实现你的目标

基本上创建文件，写文件只需要一行，而且是只需一个方法调用！
下面的例子创建并且写了6个不同的文件来展示是怎么使用的

````java
Charset utf8 = StandardCharsets.UTF_8;
List<String> lines = Arrays.asList("1st line", "2nd line");
byte[] data = {1, 2, 3, 4, 5};

try {
    Files.write(Paths.get("file1.bin"), data);
    Files.write(Paths.get("file2.bin"), data,
            StandardOpenOption.CREATE, StandardOpenOption.APPEND);
    Files.write(Paths.get("file3.txt"), "content".getBytes());
    Files.write(Paths.get("file4.txt"), "content".getBytes(utf8));
    Files.write(Paths.get("file5.txt"), lines, utf8);
    Files.write(Paths.get("file6.txt"), lines, utf8,
            StandardOpenOption.CREATE, StandardOpenOption.APPEND);
} catch (IOException e) {
    e.printStackTrace();
}
````

### 其他答案（4）：
下面是一个小程序来创建和写文件。该版本的代码比较长，但是可以容易理解
````java
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStreamWriter;
import java.io.Writer;

public class writer {
    public void writing() {
        try {
            //Whatever the file path is.
            File statText = new File("E:/Java/Reference/bin/images/statsTest.txt");
            FileOutputStream is = new FileOutputStream(statText);
            OutputStreamWriter osw = new OutputStreamWriter(is);    
            Writer w = new BufferedWriter(osw);
            w.write("POTATO!!!");
            w.close();
        } catch (IOException e) {
            System.err.println("Problem writing to the file statsTest.txt");
        }
    }

    public static void main(String[]args) {
        writer write = new writer();
        write.writing();
    }
}
````




stackoverflow链接：
http://stackoverflow.com/questions/2885173/how-to-create-a-file-and-write-to-a-file-in-java
