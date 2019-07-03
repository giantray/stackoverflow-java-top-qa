# serialVersionUID 有什么作用？该如何使用？
##问题

当一个对象实现 Serializable 接口时，多数 ide 会提示声明一个静态常量 serialVersionUID(版本标识），那 serialVersionUID 到底有什么作用呢？应该如何使用 serialVersionUID ？

##回答
serialVersionUID 是实现 Serializable 接口而来的，而 Serializable 则是应用于Java 对象序列化/反序列化。对象的序列化主要有两种用途:

- 把对象序列化成字节码，保存到指定介质上(如磁盘等)
- 用于网络传输

现在反过来说就是，serialVersionUID 会影响到上述所提到的两种行为。那到底会造成什么影响呢？

[java.io.Serializable](http://docs.oracle.com/javase/7/docs/api/java/io/Serializable.html) doc 文档，给出了一个相对详细解释:

	serialVersionUID 是 Java 为每个序列化类产生的版本标识，可用来保证在反序列时，发送方发送的和接受方接收的是可兼容的对象。如果接收方接收的类的 serialVersionUID 与发送方发送的 serialVersionUID 不一致，进行反序列时会抛出 InvalidClassException。序列化的类可显式声明 serialVersionUID 的值，如下:
	```
	ANY-ACCESS-MODIFIER static final long serialVersionUID = 1L;
	```
    当显式定义 serialVersionUID 的值时，Java 根据类的多个方面(具体可参考 Java 序列化规范)动态生成一个默认的 serialVersionUID 。尽管这样，还是建议你在每一个序列化的类中显式指定 serialVersionUID 的值，因为不同的 jdk 编译很可能会生成不同的 serialVersionUID 默认值，进而导致在反序列化时抛出 InvalidClassExceptions 异常。所以，为了保证在不同的 jdk 编译实现中，其 serialVersionUID 的值也一致，可序列化的类必须显式指定 serialVersionUID 的值。另外，serialVersionUID 的修饰符最好是 private，因为 serialVersionUID 不能被继承，所以建议使用 private 修饰 serialVersionUID。


举例说明如下:
现在尝试通过将一个类 Person 序列化到磁盘和反序列化来说明  serialVersionUID 的作用: Person 类如下:
```
public class Person implements Serializable {

    private static final long serialVersionUID = 1L;

    private String name;
    private Integer age;
    private String address;

    public Person() {
    }

    public Person(String name, Integer age, String address) {
        this.name = name;
        this.age = age;
        this.address = address;
    }


    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", address='" + address + '\'' +
                '}';
    }
}
```

简单的测试一下：
```
@Test
public void testversion1L() throws Exception {
    File file = new File("person.out");
    // 序列化
    ObjectOutputStream oout = new ObjectOutputStream(new FileOutputStream(file));
    Person person = new Person("John", 21, "广州");
    oout.writeObject(person);
    oout.close();
    // 反序列化
    ObjectInputStream oin = new ObjectInputStream(new FileInputStream(file));
    Object newPerson = oin.readObject(); 
    oin.close();
    System.out.println(newPerson);
}
```

测试发现没有什么问题。有一天，因发展需要， 需要在 Person 中增加了一个字段 email，如下:
```
public class Person implements Serializable {

    private static final long serialVersionUID = 1L;

    private String name;
    private Integer age;
    private String address;
    private String email;

    public Person() {
    }

     public Person(String name, Integer age, String address) {
        this.name = name;
        this.age = age;
        this.address = address;
    }

    public Person(String name, Integer age, String address,String email) {
        this.name = name;
        this.age = age;
        this.address = address;
        this.email = email;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", address='" + address + '\'' +
                ", email='" + email + '\'' +
                '}';
    }
}
```

这时我们假设和之前序列化到磁盘的 Person 类是兼容的，便不修改版本标识 serialVersionUID。再次测试如下
```
@Test
public void testversion1LWithExtraEmail() throws Exception {
    File file = new File("person.out");
    ObjectInputStream oin = new ObjectInputStream(new FileInputStream(file));
    Object newPerson = oin.readObject(); 
    oin.close();
    System.out.println(newPerson);
}
```
将以前序列化到磁盘的旧 Person 反序列化到新 Person 类时，没有任何问题。

可当我们增加 email 字段后，不作向后兼容。即放弃原来序列化到磁盘的 Person 类，这时我们可以将版本标识提高，如下:
```
private static final long serialVersionUID = 2L;
```

再次进行反序列化，则会报错，如下:
```
java.io.InvalidClassException:Person local class incompatible: stream classdesc serialVersionUID = 1, local class serialVersionUID = 2
```

谈到这里，我们大概可以清楚，serialVersionUID 就是控制版本是否兼容的，若我们认为修改的 Person 是向后兼容的，则不修改 serialVersionUID；反之，则提高 serialVersionUID的值。再回到一开始的问题，为什么 ide 会提示声明 serialVersionUID 的值呢？

因为若不显式定义 serialVersionUID 的值，Java 会根据类细节自动生成 serialVersionUID 的值，如果对类的源代码作了修改，再重新编译，新生成的类文件的serialVersionUID的取值有可能也会发生变化。类的serialVersionUID的默认值完全依赖于Java编译器的实现，对于同一个类，用不同的Java编译器编译，也有可能会导致不同的serialVersionUID。所以 ide 才会提示声明 serialVersionUID 的值。

附录拓展:

- [深入理解 Java 对象序列化](http://developer.51cto.com/art/201202/317181.htm)
- [对象的序列化和反序列化](http://www.blogjava.net/lingy/archive/2008/10/10/233630.html)

stackoverflow原址：http://stackoverflow.com/questions/285793/what-is-a-serialversionuid-and-why-should-i-use-it
