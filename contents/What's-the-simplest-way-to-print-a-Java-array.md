输出 Java 数组最简单的方式
===
问题
---
因为 Java 数组中没有 toString() 方法，所以我如果直接调用数组toStrign()方法的话，只会得到它的内存地址。像这样，显得并不人性化:
```java
int[] intArray = new int[] {1, 2, 3, 4, 5};
System.out.println(intArray);     // 有时候会输出 '[I@3343c8b3'
```

所以输出一个数组最简单的方法是什么？我想要的效果是
```java
// 数字数组:
int[] intArray = new int[] {1, 2, 3, 4, 5};
//输出: [1, 2, 3, 4, 5]

// 对象数组:
String[] strArray = new String[] {"John", "Mary", "Bob"};
//输出: [John, Mary, Bob]
```

回答
---
在 Java 5+ 以上中使用 Arrays.toString(arr) 或 Arrays.deepToString(arr)来打印（输出）数组。

不要忘了引入import java.util.Arrays; 
```java
package packageName;
import java.util.Arrays;
```

```java
int[] intArray = new int[] {1, 2, 3, 4, 5};
System.out.println(Arrays.toString(intArray));
//输出: [1, 2, 3, 4, 5]

String[] strArray = new String[] {"John", "Mary", "Bob"};
System.out.println(Arrays.deepToString(strArray));
*//输出: [John, Mary, Bob]
```
Arrays.deepToString与Arrays.toString不同之处在于，Arrays.deepToString更适合打印多维数组<br>
比如： <br>

```java
String[][] b = new String[3][4];
		for (int i = 0; i < 3; i++)
		{
			for (int j = 0; j < 4; j++)
			{
				b[i][j] = "A" + j;
			}
		} 
		System.out.println(Arrays.toString(b));
		//输出[[Ljava.lang.String;@55e6cb2a, [Ljava.lang.String;@23245e75, [Ljava.lang.String;@28b56559]
		System.out.println(Arrays.deepToString(b));
		//输出[[A0, A1, A2, A3], [A0, A1, A2, A3], [A0, A1, A2, A3]]
		
```
stackoverflow链接： http://stackoverflow.com/questions/409784/whats-the-simplest-way-to-print-a-java-array
