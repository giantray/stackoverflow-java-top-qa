我应该用哪一个@NotNull注解?
===
我希望能通过注解的方式，尽量避免程序中出现空指针问题，同时既能保障代码的可读性，又能和IDE的代码检查，静态代码扫描工具结合起来。相关的注解，我看到有好多种@NotNull/@NonNull/@Nonnull，而他们彼此间又有冲突，不能共用，下面是我找到的一些注解，哪个是最好的选择呢？

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

我推荐用javax命名空间下的注解(虽然我喜欢Lombok和Intelij做的事情)，使用其他命名空间的注解，等于你还需要引入其他依赖。

我用javax.validation.constraints.NotNull，因为它已经在Java EE 6中定义

javax.annotation.NonNull可能直到java 8都不存在(正如Stephen指出)。其他的都不是标准的注解 .

如果注解是可扩展的，那将是一件美好的事情.你可以自己写一个`non-null`注解，然后继承上面说的这些注解。如果标准的注解不支持某个特性，你就可以在自己定义的注解里面扩展。





stackoverflow链接： http://stackoverflow.com/questions/4963300/which-notnull-java-annotation-should-i-use




	