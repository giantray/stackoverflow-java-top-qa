## @Component, @Repository, @Service的区别

#### 问题

在spring集成的框架中，注解在类上的`@Component`，`@Repository`，`@Service`等注解能否被互换？或者说这些注解有什么区别？

#### 回答1

引用spring的官方文档中的一段描述：

在Spring2.0之前的版本中，`@Repository`注解可以标记在任何的类上，用来表明该类是用来执行与数据库相关的操作（即dao对象），并支持自动处理数据库操作产生的异常

在Spring2.5版本中，引入了更多的Spring类注解：`@Component`,`@Service`,`@Controller`。`Component`是一个通用的Spring容器管理的单例bean组件。而`@Repository`, `@Service`, `@Controller`就是针对不同的使用场景所采取的特定功能化的注解组件。

因此，当你的一个类被`@Component`所注解，那么就意味着同样可以用`@Repository`, `@Service`, `@Controller`来替代它，同时这些注解会具备有更多的功能，而且功能各异。

最后，如果你不知道要在项目的业务层采用`@Service`还是`@Component`注解。那么，`@Service`是一个更好的选择。

就如上文所说的，`@Repository`早已被支持了在你的持久层作为一个标记可以去自动处理数据库操作产生的异常（译者注：因为原生的java操作数据库所产生的异常只定义了几种，但是产生数据库异常的原因却有很多种，这样对于数据库操作的报错排查造成了一定的影响；而Spring拓展了原生的持久层异常，针对不同的产生原因有了更多的异常进行描述。所以，在注解了`@Repository`的类上如果数据库操作中抛出了异常，就能对其进行处理，转而抛出的是翻译后的spring专属数据库异常，方便我们对异常进行排查处理）。

| 注解      | 含义           |
| ------------- |:-------------:|
| @Component | 最普通的组件，可以被注入到spring容器进行管理 |
| @Repository | 作用于持久层 | 
| @Service | 作用于业务逻辑层 |
| @Controller | 作用于表现层（spring-mvc的注解） |

#### 回答2

这几个注解几乎可以说是一样的：因为被这些注解修饰的类就会被Spring扫描到并注入到Spring的bean容器中。

这里，有两个注解是不能被其他注解所互换的：

* `@Controller` 注解的bean会被spring-mvc框架所使用。
* `@Repository` 会被作为持久层操作（数据库）的bean来使用

如果想使用自定义的组件注解，那么只要在你定义的新注解中加上`@Component`即可：

```java
@Component 
@Scope("prototype")
public @interface ScheduleJob {...}
```

这样，所有被`@ScheduleJob`注解的类就都可以注入到spring容器来进行管理。我们所需要做的，就是写一些新的代码来处理这个自定义注解（译者注：可以用反射的方法），进而执行我们想要执行的工作。

#### 回答3

`@Component`就是跟`<bean>`一样，可以托管到Spring容器进行管理。

@Service, @Controller , @Repository = {@Component + 一些特定的功能}。这个就意味着这些注解在部分功能上是一样的。

当然，下面三个注解被用于为我们的应用进行分层：

* `@Controller`注解类进行前端请求的处理，转发，重定向。包括调用Service层的方法
* `@Service`注解类处理业务逻辑
* `@Repository`注解类作为DAO对象（数据访问对象，Data Access Objects），这些类可以直接对数据库进行操作

有这些分层操作的话，代码之间就实现了松耦合，代码之间的调用也清晰明朗，便于项目的管理；假想一下，如果只用`@Controller`注解，那么所有的请求转发，业务处理，数据库操作代码都糅合在一个地方，那这样的代码该有多难拓展和维护。

#### 总结

* `@Component`, `@Service`, `@Controller`, `@Repository`是spring注解，注解后可以被spring框架所扫描并注入到spring容器来进行管理
* `@Component`是通用注解，其他三个注解是这个注解的拓展，并且具有了特定的功能
* `@Repository`注解在持久层中，具有将数据库操作抛出的原生异常翻译转化为spring的持久层异常的功能。
* `@Controller`层是spring-mvc的注解，具有将请求进行转发，重定向的功能。
* `@Service`层是业务逻辑层注解，这个注解只是标注该类处于业务逻辑层。
* 用这些注解对应用进行分层之后，就能将请求处理，义务逻辑处理，数据库操作处理分离出来，为代码解耦，也方便了以后项目的维护和开发。

#### Stackoverflow链接：

[http://stackoverflow.com/questions/6827752/whats-the-difference-between-component-repository-service-annotations-in](http://stackoverflow.com/questions/6827752/whats-the-difference-between-component-repository-service-annotations-in)

#### 拓展

1. [Spring注解@Component、@Repository、@Service、@Controller区别](http://www.cnblogs.com/JAYIT/p/5593169.html)
2. [Spring注解@Autowired、@Resource区别](http://www.cnblogs.com/leiOOlei/p/3713779.html)