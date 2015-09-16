# 输出 Java 数组最简单的方式

## 问题
因为 Java 数组中没有 toString() 方法，所以直接输出如下数组，输出显得并人性化:
```
int[] intArray = new int[] {1, 2, 3, 4, 5};
System.out.println(intArray);     // prints something like '[I@3343c8b3'
```

有什么方式可以实现如下的输出效果?
```
// array of primitives:
int[] intArray = new int[] {1, 2, 3, 4, 5};
//output: [1, 2, 3, 4, 5]

// array of object references:
String[] strArray = new String[] {"John", "Mary", "Bob"};
//output: [John, Mary, Bob]
```

## 解答

在 Java 5+ 以上，可以使用 Arrays.toString(arr) 输出数组的值，使用 Arrays.deepToString(arr) 输出多维数组的值.提示，数组中的对象都会调用 toString() 方法输出其值。

首先，引入对应的包:
```
package packageName;
import java.util.Arrays;
...
```

例子:
```
// simple array
String[] array = new String[] {"John", "Mary", "Bob"};
System.out.println(Arrays.toString(array));
//output: [John, Mary, Bob]

// nested array
String[][] deepArray = new String[][] {{"John", "Mary"}, {"Alice", "Bob"}};
System.out.println(Arrays.toString(deepArray));
//output: [[Ljava.lang.String;@106d69c, [Ljava.lang.String;@52e922]
System.out.println(Arrays.deepToString(deepArray));
//output: [[John, Mary], [Alice, Bob]]
```

对于原始类型的数组，其本质就是将原始类型转换为对应包装类型，然后调用其 toString() 方法输出其值。

stackoverflow原址：http://stackoverflow.com/questions/409784/whats-the-simplest-way-to-print-a-java-array