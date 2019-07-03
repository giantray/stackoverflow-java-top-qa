##在java中如何对比（compare）string

- `==`对应的是指针相等，也就是他们是否为同一个对象
- `.equals()`对应的是值相等，也就是逻辑相等

因此，如果你想检查两个字符串是否为相同值，那么应该用`.equals()`方法
```java
//值是相等的
new String("test").equals("test") // --> true 

// ... 值相等，但不是同个对象(指向不同的地址空间）
new String("test") == "test" // --> false 

// ... 同上
new String("test") == new String("test") // --> false 

// 这个返回true，是因为这种写法属于字符串字面量，编译器会维护一个常量池，相同的字面量，都会指向相同的一个对象
"test" == "test" // --> true 

```
因此， 值的对比，一般都是用equals方法。字符串字面量之间的对比，也可以用==（大家知其所以然即可，但没必要用==）

下面多举个字符串字面量的例子,下面代码中，前四个对比，返回true，最后一个返回false。
``` java
	public static final String test1 = "test";
	public static final String test2 = "test";

	@Test
	public void test() {

		String test3 = "test";
		String test = "test";

		System.out.println(test3.equals(test));
		System.out.println(test3 == test);
		System.out.println(test1.equals(test2));
		System.out.println(test1 == test2);
		System.out.println(test1 == new String("test"));
	}
```

###其他
- 如果你重写了equal方法，记得相对应地修改hashcode方法，否则将会违反这两个方法的对等关系，如果两个对象是相等（equal）的，那么两个对象调用hashCode必须产生相同的整数结果，即：equal为true，hashCode必须为true，equal为false，hashCode也必须为false
- 如果要忽略大小写进行对比，可以用equalsIgnoreCase()方法
