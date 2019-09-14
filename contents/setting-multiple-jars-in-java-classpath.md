##如何在classpath中设置多个jar包？

###问题
是否有一个方法可以在classpath选项中包含一个文件夹（目录）下的所有jar包？
我尝试运行`java -classpath lib/*.jar:. my.package.Program`，但是无法找到相应的类文件，可是这些类文件确实存在于命令中的jar包中。我是否需要在classpath中分别指定所有的jar包？

###回答
在使用Java6或者以上版本时，classpath选项可以支持通配符(wildcards)。使用方法如下：
* 使用直接引用(`"`)
* 使用 `*` 而不是 `*.jar`

**Windows平台**

`java -cp "Test.jar;lib/*" my.package.MainClass`

**Unix平台**

`java -cp "Test.jar:lib/*" my.package.MainClass`

Unix平台与Windows平台基本一样，除了使用冒号 `:` 替代分号 `;` 之外。如果你不能使用通配符，也可以使用`bash`完成上述功能，命令如下（其中lib是一个包含所有jar包的目录）：
`java -cp $(echo lib/*.jar | tr ' ' ':')`

注意事项：classpath选项与-jar选项并不能兼容。可以查看：[Execute jar file with multiple classpath libraries from command prompt](http://stackoverflow.com/questions/13018100/execute-jar-file-with-multiple-classpath-libraries-from-command-prompt)

**对于通配符的理解**
来自[Classpath](http://java.sun.com/javase/6/docs/technotes/tools/windows/classpath.html)文档：

类路径可以包含一个基本文件名通配符`*`，其等价于指定一个特定目录下的所有以.jar或.JAR为后缀的文件的列表。例如，一个类路径的条目为`foo/*`，其指定了foo目录下的所有jar文件。一个仅仅包含`*`的classpath条目(entry)指定了当前目录下的所有jar包。

一个包含了`*`的classpath条目不能匹配特定目录下的class文件。为了既能匹配foo目录下的类文件，也能匹配jar包，可以使用`foo;foo/*`或`foo/*;foo`。对于前者而言，类文件和资源选择的顺序先是foo目录下的类文件和资源，之后才是jar包；而后者则正好相反。

通配符并不能递归地搜索子目录下的jar包。例如，`foo/*`只找`foo`目录下的jar包，而不会寻找`foo/bar`，`foo/baz`等目录下的jar包。

一个目录中的jar包枚举顺序并不固定，这不仅和平台有关，甚至可能会在同一个机器上因为时间不同而表现不同。一个结构良好（well-constructed）的应用不应该依赖于某个特定的顺序。如果特定顺序是不可避免的时候，就需要在classpath中显示枚举所有的jar包了。

在类加载进程中，通配符的扩展在早期完成，优先于程序main函数的调用，而不是在其后。每个包含通配符的类路径都被替换为所在目录下所有jar包的序列。例如，如果目录`foo`包含`a.jar`，`b.jar`以及`c.jar`，因此类路径`foo/*`被扩展为`foo/a.jar;foo/b.jar;foo/c.jar`，并且以上字符串被作为系统属性`java.class.path`的值。

环境变量`CLASSPATH`与命令行选项-classpath或者-cp并没有什么不同。也就是说，通配符既可以应用于命令行`-classpath/-cp`选项中，也可以应用于环境变量`CLASSPATH`中。

stackoverflow原地址：http://stackoverflow.com/questions/219585/setting-multiple-jars-in-java-classpath