## JSF, Servlet 和 JSP (三种技术)有什么区别？

###问题
JSP 和 Servlet 有什么关系？JSP 是某种 Servlet 吗？JSP 和 JSF 又有什么关系？JSF 是某种基于JSP的，预构建好的 UI 吗，像
ASP.NET-MVC 那样？


###回答1

#### JSP(Java Server Pages)
JSP 是一种运行在服务器上的Java 视图技术，它允许你写入模版化的文本(例如客户端代码 HTML, CSS, JavaScript等)。JSP 支持标签库(taglibs)，标签库由Java 代码实现，让你可以动态地控制页面输出。JSTL 便是一种比较有名的标签库。JSP 同样支持表达式语言(expression language)，表达式语言可以用来访问后台数据(页面上可用的属性，request/session 对象等等), 通常与标签库结合使用。

当一个 JSP 第一次被访问或者 webapp 启动时，servlet 容器会将 JSP 编译成一个继承了 HttpServlet 的类，然后在整个 webapp 生命周期内使用被编译后的类。可以在 servlet 容器的 work 目录下找到 JSP 对应的源代码。例如Tomcat 的 CATALINA.BASE/work 目录。
当收到一个 JSP 请求时，servlet 容器会执行编译 JSP 生成的类，并将该类的输出(通常是 HTML/CSS/JS)发送到 客户端，客户端(WEB 浏览器) 会展示从服务端收到的内容。

#### Servlet
Servlet 是一种针对服务器端的 API，它用来响应客户端请求，并生成响应。比较有名的例子是 HttpServlet，它提供了响应 HTTP 请求(例如 GET POST)的方法。你可以从 web.xml 配置 HttpServlet 来监听某种 HTTP URL pattern 的请求，或者使用较新的 Java EE 6 @WebServlet 注解。

当 Servlet 第一次被请求，或者 webapp 启动时，servlet 容器会创建该 Servlet 的实例，并在整个 webapp 的生命周期维持该实例在内存中。同一个实例会被复用，来响应匹配到 URL pattern 的请求。可以通过 HttpServletRequest 访问请求里的数据，通过 HttpServletResponse 控制响应。上边两个对象会是 HttpServlet 的重载方法 doGet()和 doPost() 的参数。

#### JSF (JavaServer Faces)
JSF 是一个基于组件的MVC框架，建立在 Servlet API 基础上，JSF 通过标签库提供组件，标签库又可以用于 JSP 或者其它 Java 视图技术例如 Facelets. Facelets 更适合JSF。即它提供了很厉害的模版功能例如组合组件，而JSP 基本上只提供了 `<jsp:include>` 来支持模版，所以
当你想用一个组件替换一组重复出现的组件时，你不得不使用原生的 Java 代码来创建自定义组件(这在 JSF 里并不那么清晰明了，而且带来很多冗余工作)。为了推进 Facelets，自从 JSF 2.0 之后，JSP 这种视图技术已经被废弃了。
作为一种 MVC(Model-View-Controller)框架，JSF 提供了唯一的 FacesServlet 请求/响应控制器。它负责所有的 HTTP 请求/响应工作，
例如 收集/校验/转换用户输入，将输入设置到 model 对象里，调用处理逻辑并输出响应。这样你基本上 只有一个 JSP或者 Facelets(XHTML) 页面用作视图，再加一个 Javabean 类当作 模型。 JSF 组件用来将模型和视图绑定起来(类似 ASP.NET web control 做的)，然后 FacesServlet 使用 JSF 组件树来完成整个工作。

### 其它答案选编

参考以下链接

[http://www.oracle.com/technetwork/java/faq-137059.html](http://www.oracle.com/technetwork/java/faq-137059.html)

[https://jcp.org/en/introduction/faq](https://jcp.org/en/introduction/faq)

JSP 是一种特殊的Servlet。

JSF 是一个可以配合 JSP 使用的标签集。


### stackoverflow原文链接：
[http://stackoverflow.com/questions/2095397/what-is-the-difference-between-jsf-servlet-and-jsp](http://stackoverflow.com/questions/2095397/what-is-the-difference-between-jsf-servlet-and-jsp)





