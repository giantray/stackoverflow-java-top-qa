# Java 源码里的设计模式

从 [维基百科](https://en.wikipedia.org/wiki/Software_design_pattern#Classification_and_list) 中,可以让你对大部分设计模式有一个概览,而且它也指出了哪些设计模式是 GoF 中规范.下面列出可以从 JavaSE 和 JavaEE API 中找到的设计模式:

## [创建型模式](https://en.wikipedia.org/wiki/Creational_pattern)

### [抽象工厂](http://en.wikipedia.org/wiki/Abstract_factory_pattern)

- [javax.xml.parsers.DocumentBuilderFactory#newInstance()](http://docs.oracle.com/javase/6/docs/api/javax/xml/parsers/DocumentBuilderFactory.html#newInstance%28%29)
- [javax.xml.transform.TransformerFactory#newInstance()](http://docs.oracle.com/javase/6/docs/api/javax/xml/transform/TransformerFactory.html#newInstance%28%29)
- [javax.xml.xpath.XPathFactory#newInstance()](http://docs.oracle.com/javase/6/docs/api/javax/xml/xpath/XPathFactory.html#newInstance%28%29)

### [建造者模式](http://en.wikipedia.org/wiki/Builder_pattern)

- [java.lang.StringBuilder#append()](http://docs.oracle.com/javase/6/docs/api/java/lang/StringBuilder.html#append%28boolean%29)(非同步)
- [java.lang.StringBuffer#append()](http://docs.oracle.com/javase/6/docs/api/java/lang/StringBuffer.html#append%28boolean%29)(同步)
- [java.nio.ByteBuffer#put()](http://docs.oracle.com/javase/6/docs/api/java/nio/ByteBuffer.html#put%28byte%29)(类似的还有, [CharBuffer](http://docs.oracle.com/javase/6/docs/api/java/nio/CharBuffer.html#put%28char%29), [ShortBuffer](http://docs.oracle.com/javase/6/docs/api/java/nio/ShortBuffer.html#put%28short%29), [IntBuffer](http://docs.oracle.com/javase/6/docs/api/java/nio/IntBuffer.html#put%28int%29), [LongBuffer](http://docs.oracle.com/javase/6/docs/api/java/nio/LongBuffer.html#put%28long%29), [FloatBuffer](http://docs.oracle.com/javase/6/docs/api/java/nio/FloatBuffer.html#put%28float%29) 和 [DoubleBuffer](http://docs.oracle.com/javase/6/docs/api/java/nio/DoubleBuffer.html#put%28double%29))
- [javax.swing.GroupLayout.Group#addComponent()](http://docs.oracle.com/javase/6/docs/api/javax/swing/GroupLayout.Group.html#addComponent%28java.awt.Component%29)

### [工厂模式](http://en.wikipedia.org/wiki/Factory_method_pattern)

- [java.util.Calendar#getInstance()](http://docs.oracle.com/javase/6/docs/api/java/util/Calendar.html#getInstance%28%29)
- [java.util.ResourceBundle#getBundle()](http://docs.oracle.com/javase/6/docs/api/java/util/ResourceBundle.html#getBundle%28java.lang.String%29)
- [java.text.NumberFormat#getInstance()](http://docs.oracle.com/javase/6/docs/api/java/text/NumberFormat.html#getInstance%28%29)
- [java.nio.charset.Charset#forName()](http://docs.oracle.com/javase/6/docs/api/java/nio/charset/Charset.html#forName%28java.lang.String%29)
- [java.net.URLStreamHandlerFactory#createURLStreamHandler(String)](http://docs.oracle.com/javase/6/docs/api/java/net/URLStreamHandlerFactory.html)

### [原型模式](http://en.wikipedia.org/wiki/Prototype_pattern)

- [java.lang.Object#clone()](http://docs.oracle.com/javase/6/docs/api/java/lang/Object.html#clone%28%29)(类需要实现 [java.lang.Cloneable](http://docs.oracle.com/javase/6/docs/api/java/lang/Cloneable.html) 接口)

### [单例模式](http://en.wikipedia.org/wiki/Singleton_pattern)

- [java.lang.Runtime#getRuntime()](http://docs.oracle.com/javase/6/docs/api/java/lang/Runtime.html#getRuntime%28%29)
- [java.awt.Desktop#getDesktop()](http://docs.oracle.com/javase/6/docs/api/java/awt/Desktop.html#getDesktop%28%29)
- [java.lang.System#getSecurityManager()](http://docs.oracle.com/javase/6/docs/api/java/lang/System.html#getSecurityManager%28%29)

## [结构型模式](http://en.wikipedia.org/wiki/Structural_pattern)

### [适配器模式](http://en.wikipedia.org/wiki/Adapter_pattern)

- [java.util.Arrays#asList()](http://docs.oracle.com/javase/6/docs/api/java/util/Arrays.html#asList%28T...%29)
- [java.io.InputStreamReader(InputStream) ](http://docs.oracle.com/javase/6/docs/api/java/io/InputStreamReader.html#InputStreamReader%28java.io.InputStream%29)(返回 Reader)
- [java.io.OutputStreamWriter(OutputStream)](http://docs.oracle.com/javase/6/docs/api/java/io/OutputStreamWriter.html#OutputStreamWriter%28java.io.OutputStream%29)(返回 Writer)
- [javax.xml.bind.annotation.adapters.XmlAdapter#marshal()](http://docs.oracle.com/javase/6/docs/api/javax/xml/bind/annotation/adapters/XmlAdapter.html#marshal%28BoundType%29) 和 [#unmarshal()](http://docs.oracle.com/javase/6/docs/api/javax/xml/bind/annotation/adapters/XmlAdapter.html#unmarshal%28ValueType%29)

### [桥模式](http://en.wikipedia.org/wiki/Bridge_pattern)
暂时没有发现

### [合成模式](http://en.wikipedia.org/wiki/Composite_pattern)

- [java.awt.Container#add(Component)](http://docs.oracle.com/javase/6/docs/api/java/awt/Container.html#add%28java.awt.Component%29)(Swing 中几乎所有类都使用)
- [javax.faces.component.UIComponent#getChildren()](http://docs.oracle.com/javaee/6/api/javax/faces/component/UIComponent.html#getChildren%28%29)(JSF UI 中几乎所有类都使用)

### [装饰模式](http://en.wikipedia.org/wiki/Decorator_pattern)

- [java.io.InputStream](http://docs.oracle.com/javase/6/docs/api/java/io/InputStream.html),[OutputStream](http://docs.oracle.com/javase/6/docs/api/java/io/OutputStream.html),[Reader](http://docs.oracle.com/javase/6/docs/api/java/io/Reader.html) 和 [Writer](http://docs.oracle.com/javase/6/docs/api/java/io/Writer.html) 的所有资料都有一个使用 InputStream,OutputStream,Reader,Writer 的构造器
- [java.util.Collections](http://docs.oracle.com/javase/6/docs/api/java/util/Collections.html) 中的 [checkedXXX()](http://docs.oracle.com/javase/6/docs/api/java/util/Collections.html#checkedCollection%28java.util.Collection,%20java.lang.Class%29), [synchronizedXXX()](http://docs.oracle.com/javase/6/docs/api/java/util/Collections.html#synchronizedCollection%28java.util.Collection%29) 和 [unmodifiableXXX()](http://docs.oracle.com/javase/6/docs/api/java/util/Collections.html#unmodifiableCollection%28java.util.Collection%29) 方法
- [javax.servlet.http.HttpServletRequestWrapper](http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequestWrapper.html) 和 [HttpServletResponseWrapper](http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletResponseWrapper.html)

### [门面模式](http://en.wikipedia.org/wiki/Facade_pattern)
[javax.faces.context.FacesContext](http://docs.oracle.com/javaee/6/api/javax/faces/context/FacesContext.html),其内部使用了 [LifeCycle](http://docs.oracle.com/javaee/6/api/javax/faces/lifecycle/Lifecycle.html), [ViewHandler](http://docs.oracle.com/javaee/6/api/javax/faces/application/ViewHandler.html), [NavigationHandler](http://docs.oracle.com/javaee/6/api/javax/faces/application/NavigationHandler.html) 等接口或抽象类，没有这一个门面类，终端就需要考虑如何去使用接口或抽象类（实际上不需要，因为门面类通过反射完成了）
[javax.faces.context.ExternalContext](http://docs.oracle.com/javaee/6/api/javax/faces/context/ExternalContext.html), 其内部使用了 [ServletContext](http://docs.oracle.com/javaee/6/api/javax/servlet/ServletContext.html), [HttpSession](http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpSession.html), [HttpServletRequest](http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html), [HttpServletResponse](http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletResponse.html) 等

### [享元模式](http://en.wikipedia.org/wiki/Flyweight_pattern)

- [java.lang.Integer#valueOf(int)](http://docs.oracle.com/javase/6/docs/api/java/lang/Integer.html#valueOf%28int%29)，类似得还有 [Boolean](http://docs.oracle.com/javase/6/docs/api/java/lang/Boolean.html#valueOf%28boolean%29), [Byte](http://docs.oracle.com/javase/6/docs/api/java/lang/Byte.html#valueOf%28byte%29), [Character](http://docs.oracle.com/javase/6/docs/api/java/lang/Character.html#valueOf%28char%29), [Short](http://docs.oracle.com/javase/6/docs/api/java/lang/Short.html#valueOf%28short%29) 和 [Long](http://docs.oracle.com/javase/6/docs/api/java/lang/Long.html#valueOf%28long%29)

### [代理模式](http://en.wikipedia.org/wiki/Proxy_pattern)

- [java.lang.reflect.Proxy](http://docs.oracle.com/javase/6/docs/api/java/lang/reflect/Proxy.html)
- [java.rmi.*](http://docs.oracle.com/javase/6/docs/api/java/rmi/package-summary.html)(所有 api)

## [表现型模式](http://en.wikipedia.org/wiki/Behavioral_pattern)

### [责任链模式](http://en.wikipedia.org/wiki/Chain_of_responsibility_pattern)

- [java.util.logging.Logger#log()](http://docs.oracle.com/javase/6/docs/api/java/util/logging/Logger.html#log%28java.util.logging.Level,%20java.lang.String%29)
- [javax.servlet.Filter#doFilter()](http://docs.oracle.com/javaee/6/api/javax/servlet/Filter.html#doFilter%28javax.servlet.ServletRequest,%20javax.servlet.ServletResponse,%20javax.servlet.FilterChain%29)

### [命令模式](http://en.wikipedia.org/wiki/Command_pattern)

- [所有 java.lang.Runnable 的实现](http://docs.oracle.com/javase/6/docs/api/java/lang/Runnable.html)
- [所有 javax.swing.Action 的实现](http://docs.oracle.com/javase/6/docs/api/javax/swing/Action.html)

### [解释器模式](http://en.wikipedia.org/wiki/Interpreter_pattern)

- [java.util.Pattern](http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html)
- [java.text.Normalizer](http://docs.oracle.com/javase/6/docs/api/java/text/Normalizer.html)
- [所有 java.text.Format 的子类](http://docs.oracle.com/javase/6/docs/api/java/text/Format.html)
- [所有 javax.el.ELResolver 的子类](http://docs.oracle.com/javaee/6/api/javax/el/ELResolver.html)

### [迭代模式](http://en.wikipedia.org/wiki/Iterator_pattern)

- [所有 java.util.Iterator 的实现](http://docs.oracle.com/javase/6/docs/api/java/util/Iterator.html)(因此也包含了所有 [java.util.Scanner](http://docs.oracle.com/javase/6/docs/api/java/util/Scanner.html) 的子类)
- [所有 java.util.Enumeration 的实现](http://docs.oracle.com/javase/6/docs/api/java/util/Enumeration.html)


### [中介模式](http://stackoverflow.com/questions/1673841/examples-of-gof-design-patterns-in-javas-core-libraries)

- [java.util.Timer 中的所有 scheduleXXX() 方法)](http://docs.oracle.com/javase/6/docs/api/java/util/Timer.html)
- [java.util.concurrent.Executor#execute()](http://docs.oracle.com/javase/6/docs/api/java/util/concurrent/Executor.html#execute%28java.lang.Runnable%29)
- [java.util.concurrent.ExecutorService 中的 invokeXXX() 和 submit() 方法](http://docs.oracle.com/javase/6/docs/api/java/util/concurrent/ExecutorService.html)
- [java.util.concurrent.ScheduledExecutorService 中的所有 scheduleXXX() 方法](http://docs.oracle.com/javase/6/docs/api/java/util/concurrent/ScheduledExecutorService.html)
- [java.lang.reflect.Method#invoke()](http://docs.oracle.com/javase/6/docs/api/java/lang/reflect/Method.html#invoke%28java.lang.Object,%20java.lang.Object...%29)

### [备忘录模式](http://en.wikipedia.org/wiki/Memento_pattern)

[java.util.Date](http://docs.oracle.com/javase/6/docs/api/java/util/Date.html)(setXXX 方法更新的就是其内部的 Date 的值)
[java.io.Serializable 的所有实现](http://docs.oracle.com/javase/6/docs/api/java/io/Serializable.html)
[javax.faces.component.StateHolder 的所有实现](http://docs.oracle.com/javaee/6/api/javax/faces/component/StateHolder.html)

### [观察者模式（订阅模式)](http://en.wikipedia.org/wiki/Observer_pattern)
	
[java.util.Observer](http://docs.oracle.com/javase/6/docs/api/java/util/Observer.html)/[java.util.Observable](http://docs.oracle.com/javase/6/docs/api/java/util/Observable.html)(实际应用中，很少会用到)
[java.util.EventListener 的所有实现](http://docs.oracle.com/javase/6/docs/api/java/util/EventListener.html)(几乎包含了所有 Swing 中使用到的类)
[javax.servlet.http.HttpSessionBindingListener](http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpSessionBindingListener.html)
[javax.servlet.http.HttpSessionAttributeListener](http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpSessionAttributeListener.html)
[javax.faces.event.PhaseListener](http://docs.oracle.com/javaee/6/api/javax/faces/event/PhaseListener.html)

### [状态模式](http://en.wikipedia.org/wiki/State_pattern)

[javax.faces.lifecycle.LifeCycle#execute()](http://docs.oracle.com/javaee/6/api/javax/faces/lifecycle/Lifecycle.html#execute%28javax.faces.context.FacesContext%29)(由FacesServlet控制,行为是依赖于当前JSF生命周期阶段(状态))

### [策略模式](http://en.wikipedia.org/wiki/Strategy_pattern)

[java.util.Comparator#compare()](http://docs.oracle.com/javase/6/docs/api/java/util/Comparator.html#compare%28T,%20T%29), 在 Collections#sort() 中会使用到.
[javax.servlet.http.HttpServlet](http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServlet.html),service() 和 所有 doXXX() 方法都以 HttpServletRequest 和 HttpServletResponse 作为参数，所有方法的实现都需要显式处理这两个参数(而不是持有这个变量。)
[javax.servlet.Filter#doFilter()](http://docs.oracle.com/javaee/6/api/javax/servlet/Filter.html#doFilter%28javax.servlet.ServletRequest,%20javax.servlet.ServletResponse,%20javax.servlet.FilterChain%29)

### [模板模式](http://en.wikipedia.org/wiki/Template_method_pattern)
[java.io.InputStream](http://docs.oracle.com/javase/6/docs/api/java/io/InputStream.html), [java.io.OutputStream](http://docs.oracle.com/javase/6/docs/api/java/io/OutputStream.html), [java.io.Reader](http://docs.oracle.com/javase/6/docs/api/java/io/Reader.html) 和 [java.io.Writer](http://docs.oracle.com/javase/6/docs/api/java/io/Writer.html) 的所有 非抽象 方法。
[java.util.AbstractList](http://docs.oracle.com/javase/6/docs/api/java/util/AbstractList.html), [java.util.AbstractSet](http://docs.oracle.com/javase/6/docs/api/java/util/AbstractSet.html) 和 [java.util.AbstractMap](http://docs.oracle.com/javase/6/docs/api/java/util/AbstractMap.html) 的所有 非抽象 方法。

[javax.servlet.http.HttpServlet 中 doXXX() 方法](http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServlet.html),这些方法默认返回 405 "Method Not Allowed" ，你可以自由地选择覆盖实现其中的一个或多个。

### [访问者模式](http://en.wikipedia.org/wiki/Visitor_pattern)

[javax.lang.model.element.AnnotationValue](http://docs.oracle.com/javase/6/docs/api/javax/lang/model/element/AnnotationValue.html) 和 [AnnotationValueVisitor](http://docs.oracle.com/javase/6/docs/api/javax/lang/model/element/AnnotationValueVisitor.html)
[javax.lang.model.element.Element](http://docs.oracle.com/javase/6/docs/api/javax/lang/model/element/Element.html) 和 [ElementVisitor](http://docs.oracle.com/javase/6/docs/api/javax/lang/model/element/ElementVisitor.html)
[javax.lang.model.type.TypeMirror](http://docs.oracle.com/javase/6/docs/api/javax/lang/model/type/TypeMirror.html) 和 [TypeVisitor](http://docs.oracle.com/javase/6/docs/api/javax/lang/model/type/TypeVisitor.html)
[java.nio.file.FileVisitor](http://docs.oracle.com/javase/7/docs/api/java/nio/file/FileVisitor.html) 和 [SimpleFileVisitor](http://docs.oracle.com/javase/7/docs/api/java/nio/file/SimpleFileVisitor.html)


附录拓展：

- [设计模式-百度百科](http://baike.baidu.com/link?url=_XNWwtm_SeObjikESBkyse_nfXm2HIOOkwJ1XwyVZALLU36AG36DhOMN0Utln5-nJBT6aAplJFOGXCdwQSsm3_)


- stackoverflow原址:
http://stackoverflow.com/questions/1673841/examples-of-gof-design-patterns-in-javas-core-libraries