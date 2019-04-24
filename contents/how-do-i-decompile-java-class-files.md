## 如何对Java class文件进行反编译 ##

可以用什么程序来编译class文件 会得到java代码还是JVM编译的代码？
在这个网站上性能讨论的问题上经常看到进行反编译文件来看编译器如何优化一些东西

“反编译”的艺术也可以被认为是逆向工程。虽然有时在逆向工程时你并不总是能够访问二进制文件。


没人提到 bytecodeviewer.com吗，它可以反编译为java源码和字节码（对于java源码是基于JAD）

**www.javadecompilers.com**

	它是最流行的Java编译器，用c++编写，速度很快
	比较过时，且不提供支持，不支持Java5及以后的版本
这个网站也列出了其他的工具。

正如Salvador Valencia在评论（2017年9月）中所说 javadecompiler提供了一个SaaS，您可以将.cl​​ass文件上传到云端，并返回反编译代码。
(原答案：2008年10月)
定义J2SE 5.0（Java SE 5）主要功能的JSR 176的最终版本已于2004年9月30日发布。
由Pavel Kouznetsov先生编写的着名Java反编译器JAD支持的最新Java版本是JDK 1.3。


Java Decompiler（一个快速Java反编译器）具有：
显式支持反编译和分析Java 5+“.class”文件。
一个很好的GUI：
![](https://i.imgur.com/iXIWcl8.png)

它适用于从JDK 1.1.8到JDK 1.7.0以及其他（Jikes，JRockit等）的编译器。
它具有在线实时演示版本，实际上功能齐全！你可以在页面上删除一个jar文件，看看反编译的源代码而不安装任何东西。


比如：
Procyon：开源（Apache 2）
Krakatau：开源（GPLv3）
CFR：开源（MIT）
JAD
DJ Java Decompiler
Mocha
还有很多。

这些产生Java代码，可以让你看到JVM字节码。
要查看Java源代码，请检查一些反编译器。去搜索jad。
如果要查看字节码，只需使用JDK附带的javap即可。



我试了几个，Procyon对我来是最好的。它正在积极开发中，并支持最新版Java的许多功能。

以下是我试过的其他的：
CFR
还可以，但是经常反编译失败。我会密切注意这个。还积极开发支持最新的Java功能。
Krakatau
采用不同的方法，它尝试输出等效的Java代码，而不是尝试重建原始源，这有可能使混淆代码更好。根据我的测试，它与Procyon大致相当，但仍然很高兴有不同的东西。我确实必须使用-skip命令行标志，因此它不会停止错误。积极开发，有趣的是它是用Python编写的。
JD-GUI
工作，但Procyon的输出要好得多。这是一个将Procyon输出与原始和JD-GUI进行比较的页面。 JD-GUI也可以作为Eclipse插件使用，它根本不适用于我。似乎不是开源的，发展似乎是零星的。
JAD
工作，但只支持Java 1.4及更低版本。也可以作为Eclipse插件使用。不再在开发中。


我使用JAD Decompiler。
有一个Eclipse插件，jadeclipse。这挺好用的。


JD-GUI真的很棒。你可以打开一个jar文件并浏览代码，就好像正在使用IDE一样。好东西。

