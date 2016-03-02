我应该用哪一个@NotNull注解?
===

我试图代码的可读性像我们的开发工具IDE一样进行代码检查、静态代码分析(FindBugs,Sonar)避免空指针.大多数的开发工具是不兼容的，对于@NotNull/@NonNull/@Nonnull注解,对于理解它们是非常困难的。那么那一个是最好的选择？下面是我找到的对于注解的说明：


1.javax.validation.constraints.NotNull
 
	运行时进验证，不静态分析
	

2.edu.umd.cs.findbugs.annotations.NonNull

	用于finbugs和Sonar静态分析

3.javax.annotation.Nonnull
	
	只适用FindBugs,JSR-305不适用

4.org.jetbrains.annotations.NotNull
	
	适用用于IntelliJ IDEA静态分析

5.lombok.NonNull
	
	适用Lombok项目中代码生成器。不是一个标准的占位符注解.

6.android.support.annotation.NonNull

	适用于Android项目的标记注解,位于support-annotations包中

回答
---

我只用javax命名空间下的注解(虽然我喜欢Lombok和Intelij做的事情).

我用javax.validation.constraints.NotNull，因为它已经在Java EE 6中定义

javax.annotation.NonNull可能直到java 8都不存在(正如Stephen指出)。其他的都不是标准的注解 .你可能在语法上得到帮助而不能在运行时给你帮助,对于许多项目，这是好的，但是对于我这不是合适的.

如果注解是可扩展的，那将是一件美好的事情.这样,我们会能过封装和继承定义的的null注解.当标准解决了这个问题，你就不得不重新定义你的注解。

不幸的，这种事情没有发生.




stackoverflow链接： http://stackoverflow.com/questions/4963300/which-notnull-java-annotation-should-i-use




	