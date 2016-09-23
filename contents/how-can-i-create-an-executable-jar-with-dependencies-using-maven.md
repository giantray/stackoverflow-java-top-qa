## 如何使用maven把项目及其依赖打包为可运行jar包

#### 问题

我想把java项目打包为可运行的分布式jar包。我该怎样做，才能把项目中maven所依赖的jar包导入到我的项目jar包中？

#### 回答

在`pom.xml`文件中，加入如下的插件：

```xml
<build>
  <plugins>
    <plugin>
      <artifactId>maven-assembly-plugin</artifactId>
      <configuration>
        <archive>
          <manifest>
            <!-- 这里是你的项目main函数所在的类的全限定名 -->
            <mainClass>fully.qualified.MainClass</mainClass> 
          </manifest>
        </archive>
        <descriptorRefs>
          <descriptorRef>jar-with-dependencies</descriptorRef>
        </descriptorRefs>
      </configuration>
    </plugin>
  </plugins>
</build>
```

之后，运行maven命令：

> mvn clean compile assembly:single

`clean`,`compile`,`assembly:single`任务将会依次被执行；`compile`任务必须写在`assembly:single`之前，否则打包后的jar包内将不会有你的编译代码。

（译注：执行完后，会在你的maven项目的target目录下，生成想要的jar包，而不再需要使用`mvn package`命令进行打包）

通常情况下，上述maven命令执行后会自动绑定到项目的构建阶段，从而保证了以后在执行`mvn install`命令时的jar包也会被构建。
（译注：下面是实际上完整的默认的`pom.xml`配置，只不过`<executions>`可以被省略，若省略则按照下述默认的配置执行）

```xml
<plugin>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>fully.qualified.MainClass</mainClass>
      </manifest>
    </archive>
    <descriptorRefs>
      <descriptorRef>jar-with-dependencies</descriptorRef>
    </descriptorRefs>
  </configuration>
  <executions>
    <execution>
      <id>make-assembly</id> <!-- 用于maven继承项目的聚合 -->
      <phase>package</phase> <!-- 绑定到package阶段 -->
      <goals>
        <goal>single</goal>
      </goals>
    </execution>
  </executions>
</plugin>
```

#### 拓展

怎样去运行打包后的可运行jar包？

* 对上述配置中已经指定了`main`函数所在类的jar包，打开命令行窗口，输入命令：

```java
java -jar jar包的路径/jar包的名字.jar
```

例如：

```Auto
java -jar D:\my_java_project\maven_test.jar
```

* 若在pom.xml并没有指定`main`方法所在类，那么该jar的运行应采取如下命令：

```java
java -cp jar包的路径/jar包的名字.jar main方法所在类的全限定名
```

例如:

```java
java -cp D:\my_java_project\maven_test.jar com.my.path.MainClass
```


#### StackOverflow地址

[http://stackoverflow.com/questions/574594/how-can-i-create-an-executable-jar-with-dependencies-using-maven](http://stackoverflow.com/questions/574594/how-can-i-create-an-executable-jar-with-dependencies-using-maven)