##在java中把String转换给enum类  
-------------------------------------

###问题  
假设有有个枚举类:
```java
public enum Blah 
{
    A, B, C, D
}
```
现在我想把这个String转成枚举类,比如说"A"应该等于Blash.A.该怎么做？

------
###回答1
```java
Blah A = Blah.valueOf("A");
```
这样传入"A"会返回Balsh枚举类.
###回答2
```java
Blah A =Enum.valueOf(Blah.class, "A");
```
同样可以得到该枚举类

**这两个方法都会传入的参数大小写敏感，这个例子如果传入"a"，则会报错No enum const class Blah.a.**

stackoverflow原址： http://stackoverflow.com/questions/604424/convert-a-string-to-an-enum-in-java


