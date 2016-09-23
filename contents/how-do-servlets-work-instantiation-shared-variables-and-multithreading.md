##How do servlets work? Instantiation, shared variables and multithreading
###问题：
假设，我有一个web服务器可以支持无数的servlets，对于通过这些servlets的信息，我正在获取这些servlets的上下文环境，并设置session变量。
现在，如果有两个或者更多的user用户发送请求到这个服务器，session变量会发生什么变化？session对于所有的user是公共的还是不同的user拥有不同的session。如果用户彼此之间的session是不同的，那么服务器怎么区分辨别不同的用户呢？
另外一些相似的问题，如果有N个用户访问一个具体的servlets，那么这个servlets是只在第一个用户第一次访问的时候实例化，还是为每一个用户各自实例化呢？
###答案：
####ServletContext
当servletcontainer（像tomcat）启动的时候，它会部署和加载所有的webapplications，当一个webapplication加载完成后，servletcontainer就会创建一个ServletContext，并且保存在服务器的内存中。这个webapp的web.xml会被解析，web.xml中的每个```<servlet>, <filter> and <listener>```或者通过注解```@WebServlet, @WebFilter and @WebListener```，都会被创建一次并且也保存在服务器的内存中。对于所有filter，```init()```方法会被直接触发，当servletcontainer关闭的时候，它会unload所有的webapplications,触发所有实例化的servlets和filters的```destroy()```方法,最后，servletcontext和所有的servlets，filter和listener实例都会被销毁。

####HttpServletRequest and HttpServletResponse
servletcontainer 是附属于webserver的，而这个webserver会持续监听一个目标端口的```HTTP request```请求，这个端口在开发中经常会被设置成8080，而在生产环境会被设置成80。当一个客户端（比如用户的浏览器）发送一个HTTP request，servletcontainer就会创建新的HttpServletRequest对象和HttpServletResponse对象。。。。

在有filter的情况下，```doFilter()```方法会被触发。当代码调用```chain.doFilter(request, response)```时候，请求会经过下一个过滤器filter，如果没有了过滤器，会到达servlet。在servlets的情况下，```service()```触发，然后根据```request.getMethod()```确定执行doGet()还是```doPost()```，如果当前servlet找不到请求的方法，返回405error。

request对象提供了HTTP请求所有的信息，比如request headers和request body，response对象提供了控制和发送HTTP响应的的能力，并且以你想要的方式，比如设置headers和body。当HTTP响应结束，请求和响应对象会被销毁（实际上，大多数container将会清洗到这些对象的状态然后回收这些事例以重新利用）
####httpSession
当客户端第一次访问webapp或者通过```request.getSession()```方法第一次获取httpSession
，servletcontainer 将会创建一个新的HttpSession 对象，产生一个长的唯一的ID标记session（可以通过session.getId()）,并且将这个session存储在server内存中。servletcontainer 同时会在HTTP response的Header中设置```Set-Cookie```cookie值，其中cookie name为JSESSIONID，cookie value为唯一的长ID值。

在接下来的连续请求中，客户端浏览器都要cookie通过header带回，然后servletcontainer 会根据cookie中的JSESSIONID 值，获得server内存中的对应的httpSession。

只要没超过```<session-timeout>```设定的值，httpSession对象会一直存在，```<session-timeout>```大小可以在web.xml中设定，默认是30分钟。所以如果连续30分钟之内客户端不再访问webapp，servletcontainer就会销毁对应的session。接下来的request请求即使cookies依旧存在，但是却不再有对应的session了。servletcontainer 会创建新的session。

另外一方面，session cookie在浏览器端有默认的生命时长，就是只要浏览器一直在运行，所以当浏览器关闭，浏览器端的cookie会被销毁。
####最后
- 只要webapp存在，ServletContext 一定会存在。并且ServletContext 是被所有session和request共享的。
- 只要客户端用同一个浏览器和webapp交互并且该session没有在服务端超时，HttpSession 就会一直存在。并且在同一个会话中所有请求都是共享的。
- 只有当完整的response响应到达，HttpServletRequest 和 HttpServletResponse才不再存活，并且不被共享。
- 只要webapp存在，servlet、filter和listener就会存在。他们被所有请求和会话共享。
- 只要问题中的对象存在，任何设置在ServletContext, HttpServletRequest 和 HttpSession中的属性就会存在。

####线程安全
就是说，你主要关注的是线程安全性。你应该了解到，servlets和filter是被所有请求共享的。这正是Java的美妙之处，它的多线程和不同的线程可以充分利用同样的实例instance，否则对于每一个request请求都要重复创建和调用init()和destroy()开销太大。

但是你也应该注意到，你不应该把任何请求或会话作用域的数据作为一个servlet或过滤器的实例变量。这样会被其他会话的请求共享，并且那是线程不安全的！下面的例子阐明的这点：
```
public class ExampleServlet extends HttpServlet {

    private Object thisIsNOTThreadSafe;

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        Object thisIsThreadSafe;

        thisIsNOTThreadSafe = request.getParameter("foo"); // BAD!! Shared among all requests!
        thisIsThreadSafe = request.getParameter("foo"); // OK, this is thread safe.
    } 
}
```

stackoverflow链接：

http://stackoverflow.com/questions/3106452/how-do-servlets-work-instantiation-shared-variables-and-multithreading