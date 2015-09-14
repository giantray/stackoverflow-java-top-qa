# transient 关键字的作用?

[Java 语言规范](http://docs.oracle.com/javase/specs/jls/se7/html/jls-8.html#jls-8.3.1.3)中提到,transient 关键字用来说明指定属性不进行序列化.

若要理解 transient 关键字的作用,自然需要对序列化有一定的认识.

** 序列化 ** 

序列化是用来持久化对象的状态 -- 将对象转化为字节码保存到指定的文件中.类似地,可以通过反序列化,将字节码还原为对象原有的状态.序列化是 Java 中一个比较重要的概念,因为在网络编程中会经常用到序列化与反序列化机制.一个相对若想在网络中传输,就必须转化为字节的形式.而 Serializable 接口就是用来标识某个类或接口可以转化为字节码,Serializable 可以认为是一个标识符,因为它没有任何的方法.

Serializable 允许我们将一个类转化为字节码,进而在网络传输.可是,一个类中可能存在某些敏感的信息,我们是不想在网络中传输的,这时候我们就需要借助 transient 关键字了.被 transient 关键字标识的 field,不会进行序列化.

下面通过一个例子说明 transient 关键字的作用.现假设我们需要在网络中传输 Person 类:
```
public class Person implements Serializable{

    private static final long serialVersionUID = 1L;

    private String name;
    private String certNo; // 身份证号码
    private int age;

    public Person(String name, String certNo, int age) {
        this.name = name;
        this.certNo = certNo;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", certNo='" + certNo + '\'' +
                ", age=" + age +
                '}';
    }
}
```

若不使用 transient 关键字,反序列化时输出的信息是 :
```
Person{name='tianya', certNo='12314', age=23}
```

我们知道,身份证号码属于敏感信息,并不想在网络中传输,这时我们就可以借助 transient 关键字,如下:
```
	private transient String certNo;
```

这个时候,通过反序列化获取的 Person 信息如下 :
```
Person{name='tianya', certNo='null', age=23}
```

stackoverflow原址：http://stackoverflow.com/questions/910374/why-does-java-have-transient-variables