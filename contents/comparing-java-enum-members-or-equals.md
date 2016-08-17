## 比较java枚举成员使用equal还是==

### 问题
我知道Java枚举会被编译成一个包含私有构造参数和一堆静态方法的类，当去比较两个枚举的时候，总是使用equals()方法，例如：
```java
public useEnums(SomeEnum a)
{
    if(a.equals(SomeEnum.SOME_ENUM_VALUE))
    {
        ...
    }
    ...
}
```
除此之外，我也可以使用 == 替代equals() 方法
```java
public useEnums2(SomeEnum a)
{
    if(a == SomeEnum.SOME_ENUM_VALUE)
    {
        ...
    }
    ...
}
```
我有5年以上的java编程经验，并且我想我也懂得 == 和 equals() 之间的区别，但是我仍然觉得很困惑，哪一个操作符才是我该使用的。

### 答案

二者皆对，如果你看过枚举的源码，你会发现在源码中，equals也仅仅非常简单的 == 。
我使用 == ，因为无论如何，这个左值是可以为 null的


译者补充 java.lang.Enum 中Equals 代码：
```java
public final boolean equals(Object other) {
    return this==other;
}
```


### 额外答案
#### 能在枚举中使用 == 进行判断？
答案是肯定的，因为枚举有着严格的实例化控制，所以你可以用 == 去做比较符，这个用法，在官方文档中也有明确的说明。

>JLS 8.9 Enums
>An enum type has no instances other than those defined by its enum constants.
>It is a compile-time error to attempt to explicitly instantiate an enum type. The final clone method in Enum >ensures that enum constants can never be cloned, and the special treatment by the serialization mechanism ensures >that duplicate instances are never created as a result of deserialization. Reflective instantiation of enum types >is prohibited. Together, these four things ensure that no instances of an enum type exist beyond those defined by >the enum constants.
>Because there is only one instance of each enum constant, it is permissible to use the == operator in place of the >equals method when comparing two object references if it is known that at least one of them refers to an enum ?>constant. (The equals method in Enum is a final method that merely invokes super.equals on its argument and ?>returns the result, thus performing an identity comparison.)

#### 什么时候 == 和 equals 不一样？
As a reminder, it needs to be said that generally, == is NOT a viable alternative to equals. When it is, however (such as with enum), there are two important differences to consider:
通常来说 == 不是一个 equals的一个备选方案，无论如何有2个重要的不同处需要考虑：

##### == 不会抛出 NullPointerException
```java
enum Color { BLACK, WHITE };

Color nothing = null;
if (nothing == Color.BLACK);      // runs fine
if (nothing.equals(Color.BLACK)); // throws NullPointerException
```
##### == 在编译期检测类型兼容性
```java
enum Color { BLACK, WHITE };
enum Chiral { LEFT, RIGHT };

if (Color.BLACK.equals(Chiral.LEFT)); // compiles fine
if (Color.BLACK == Chiral.LEFT);      // DOESN'T COMPILE!!! Incompatible types!
```

#### 什么时候使用 == ？
Bloch specifically mentions that immutable classes that have proper control over their instances can guarantee to their clients that == is usable. enum is specifically mentioned to exemplify.
具体来说，那些提供恰当实例控制的不可变类能够保证 == 是可用的，枚举刚好符合这个条件。

> Item 1: Consider static factory methods instead of constructors
> [...] it allows an immutable class to make the guarantee that no two equal instances exist: a.equals(b) if and only if a==b. If a class makes this guarantee, then its clients can use the == operator instead of the equals(Object) method, which may result in improved performance. Enum types provide this guarantee.

总而言之，在枚举比较上使用 == ， 因为：
1. 能正常工作
2. 更快
3. 运行时是安全的
4. 编译期也是安全的

stackoverlfow链接：http://stackoverflow.com/questions/1750435/comparing-java-enum-members-or-equals
