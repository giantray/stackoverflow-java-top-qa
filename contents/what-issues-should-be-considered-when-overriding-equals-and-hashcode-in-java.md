##重写（Override）equals和hashCode方法时应考虑的问题

###理论上讲（编程语言、数学层面）
equals() 定义了对象的相等关系（自反性、对称性、传递性）（有点抽象，更详细说明，请参考[javadoc](http://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#equals(java.lang.Object))) 。
另外，它还具有一致性（也就是说，如果一个对象没有修改，那么对象的equals方法，应总是返回相同的值），此外，o.equals(null)应当总是返回false。
hashCode()（[javadoc](http://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#hashCode()))也必须具备一致性的（也就是说，如果equal的结果没有变，那么hashcode()也应总是返回相同的值）

总的来说，这两个方法的关系：

**假如a.equals(b)，那么a.hashCode() 应等于b.hashCode()**

###实践上讲

**如果你重写了其中一个方法，那么务必重写另外一个方法**

equals()和hashCode()所计算的属性集（set of fields）应当是一样的
如何更快地重写这两个方法呢？
1. 使用[Apache Commons Lang library](http://commons.apache.org/lang/)中的[EqualsBuilder](http://commons.apache.org/proper/commons-lang/apidocs/org/apache/commons/lang3/builder/EqualsBuilder.html)、[HashCodeBuilder](http://commons.apache.org/proper/commons-lang/apidocs/org/apache/commons/lang3/builder/HashCodeBuilder.html)
```java
public class Person {
    private String name;
    private int age;
    // ...

    public int hashCode() {
        return new HashCodeBuilder(17, 31). // two randomly chosen prime numbers
            // if deriving: appendSuper(super.hashCode()).
            append(name).
            append(age).
            toHashCode();
    }

    public boolean equals(Object obj) {
       if (!(obj instanceof Person))
            return false;
        if (obj == this)
            return true;

        Person rhs = (Person) obj;
        return new EqualsBuilder().
            // if deriving: appendSuper(super.equals(obj)).
            append(name, rhs.name).
            append(age, rhs.age).
            isEquals();
    }
}
```

2. 如果你是用eclipse，可以在代码编辑区右键，然后选择 Source > Generate hashCode() and equals()

**另外请记得**


当你使用一些基于Hash的 Collection 、 Map，例如HashSet, LinkedHashSet, HashMap, Hashtable, 、WeakHashMap等。在键值对被放到集合中之后，请确保其key值所对应的hashCode()是保持不变的。比较可靠的一个办法，是保持这些key是不可变的，这也能带来不少好处


stackoverflow链接：
http://stackoverflow.com/questions/27581/what-issues-should-be-considered-when-overriding-equals-and-hashcode-in-java
