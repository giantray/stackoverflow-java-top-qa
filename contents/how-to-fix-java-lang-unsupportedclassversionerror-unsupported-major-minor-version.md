
##如何修复 Java.lang.UnsupportedClassVersionError: Unsupported major.minor version


##问题描述

我正在使用Notepad++ 做编辑、运行、编译一体化的工具。
我已经装了JRE并且设置好了环境变量。
当我在nodepad++上运行“Hello world”，我看到了以下信息：
java.lang.UnsupportedClassVersionError: test_hello_world :
 Unsupported major.minor version 51.0
    at java.lang.ClassLoader.defineClass1(Native Method)
    at java.lang.ClassLoader.defineClassCond(Unknown Source)
       .........................................

我想这是一个版本问题，java的版本太老或者太新。
1.怎么修复？
2.我需要装jdk并且设置我的环境变量而不是设置JRE？
3.设置JRE和JDK的环境变量有什么不同？


##回答

所示版本号表示类文件可兼容的JRE版本

主要的版本号有：
Java SE 14 = 58,
Java SE 13 = 57,
Java SE 12 = 56,
Java SE 11 = 55,
Java SE 10 = 54,
Java SE 9 = 53,
Java SE 8 = 52,
Java SE 7 = 51,
Java SE 6.0 = 50,
Java SE 5.0 = 49,
JDK 1.4 = 48,
JDK 1.3 = 47,
JDK 1.2 = 46,
JDK 1.1 = 45
（来源：Wikipedia）

修复这个问题你需要使用更新版本的JAVA JRE来运行你的java代码，或者为java指定具体的target参数以指示编译器创建与早期Java版本兼容的代码。

例如，创建java 1.4的可执行class文件，使用命令行： javac -target 1.4 HelloWorld.java

对于较新版本的Java编译器，可能会收到一条关于未设置引导类路径的警告。关于这个错误的更多信息可以看博客文章（https://blogs.oracle.com/darcy/new-javac-warning-for-setting-an-older-source-without-bootclasspath）

stackoverflow原地址 https://stackoverflow.com/questions/10382929/how-to-fix-java-lang-unsupportedclassversionerror-unsupported-major-minor-version