# 比较java中枚举成员是用“==”还是equals()

## 问题
java枚举被编译成带有私有构造器和一堆public的静态成员的类。当比较枚举中的两个成员时，经常使用.equals()方法，例如
    public useEnums(SomeEnum a)
	{
    	if(a.equals(SomeEnum.SOME_ENUM_VALUE))
    	{
    	    ...
    	}
    	...
	}
然而，偶然间使用“==”代替equals方法
    public useEnums2(SomeEnum a)
	{
    	if(a == SomeEnum.SOME_ENUM_VALUE)
    	{
        	...
    	}
    	...
	}
应该使用哪个呢？
## 解答
### 回答1
技术上来说，都是对的，如果你看了equals的源码，它简单地遵从“==”	，然而我一般使用“==” 因为它对于空指针，比较安全
### 回答2
#### 能在枚举时使用用“==”吗？

可以，枚举值有小型实例控制，允许你用“==”去比较实例，在文档中有说明：

JLS 8.9 枚举
一个枚举类型除了定义的那些枚举常量外没有其他实例了。
试图明确地说明一种枚举类型是会导致编译期异常。在枚举中final clone方法确保枚举常量从不会被克隆，而且序列化机制会确保从不会因为反序列化而创造复制的实例。枚举类型的反射实例化也是被禁止的。总之，以上内容确保了除了定义的枚举常量之外，没有枚举类型实例。

因为每个枚举常量只有一个实例，所以如果在比较两个参考值，至少有一个涉及到枚举常量时，允许使用“==”代替equals()。（equals()方法在枚举类中是一个final方法，在参数和返回结果时，很少调用父类的equals()方法，因此是一种恒等的比较。）

这足够强力地支持Josh的建议，如果你坚持使用单例模式，最好的方法是用枚举类型强化单例属性（见Effective Java第二版中的第三条：用私有构造器或者枚举类型强化Singleton属性，或者单例模式的线程安全
>http://stackoverflow.com/questions/2912281/thread-safety-in-singleton/ ）

#### “==”和equals的区别
通常情况下，==并不是可以替换equals，然而在枚举中是可以的。它们之间有两个重要的不同：

 “==”从不会抛出空指针异常

    
	enum Color { BLACK, WHITE };
	
	Color nothing = null;
	
	if (nothing == Color.BLACK);      // 正常运行
	
	if (nothing.equals(Color.BLACK)); // 抛出空指
	针异常

在编译期，"=="会检查其类型的兼容性

    enum Color { BLACK, WHITE };
    enum Chiral { LEFT, RIGHT };
    
    if (Color.BLACK.equals(Chiral.LEFT)); // 编译正常
    if (Color.BLACK == Chiral.LEFT);  // 无法编译，类型不兼容

#### 在适用时，“==”可以被使用吗

Bloch（effective java的作者）明确指出不可变类可以控制它们实例保证客户端“==”是可用的。枚举就被明确地证明了

考虑静态工厂方法代替构造器
它使得不可变的类可以确保不会存在两个相等的实例，即当且仅当a==b的时候才有a.equals(b)为true。如果类保证了这一点，它的客户端可以使用“==”操作符来代替equals（Object）方法，这样可以提升性能。枚举类型保证了这一点

总之，在枚举中使用“==”优势：

- 能运行
- 更快
- 在运行期更安全
- 在编译期更安全

备注：强有力的反击了那些认为foo.equals(bar)比foo==bar更有可读性的人们。

原文地址：
> http://stackoverflow.com/questions/1750435/comparing-java-enum-members-or-equals