java中打印一个数组最简单的方法是什么
===
问题
---
在java中,数组没有重写toString()方法，所以我如果直接调用数组toStrign()方法的话，我会知道得到它的内存地址。像这样:
```java
int[] intArray = new int[] {1, 2, 3, 4, 5};
System.out.println(intArray);     // 有时候会输出 '[I@3343c8b3'
```
但是实际上我想要的输出效果是
```java
[1, 2, 3, 4, 5]
```
所以打印一个数组最简单的方法是什么？我想要的效果是
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
在JAVA5中使用 Arrays.toString(arr) 或 Arrays.deepToString(arr)来打印数组。

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
