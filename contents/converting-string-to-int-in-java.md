##如何将String转换为Int

有两种方式

```java
Integer x = Integer.valueOf(str);
// or
int y = Integer.parseInt(str);
```
这两种方式有一点点不同：
- `valueOf`返回的是`java.lang.Integer`的实例
- `parseInt`返回的是基本数据类型 int

`Short.valueOf/parseShort`,`Long.valueOf/parseLong`等也是有类似差别。

另外还需注意的是，在做int类型转换时，可能会抛出NumberFormatException，因此要做好异常捕获
```java
int foo;
String StringThatCouldBeANumberOrNot = "26263Hello"; //will throw exception
String StringThatCouldBeANumberOrNot2 = "26263"; //will not throw exception
try {
      foo = Integer.parseInt(StringThatCouldBeANumberOrNot);
} catch (NumberFormatException e) {
      //Will Throw exception!
      //do something! anything to handle the exception.
}

try {
      foo = Integer.parseInt(StringThatCouldBeANumberOrNot2);
} catch (NumberFormatException e) {
      //No problem this time but still it is good practice to care about exceptions.
      //Never trust user input :)
      //do something! anything to handle the exception.
}
```

stackoverflow链接：http://stackoverflow.com/questions/5585779/converting-string-to-int-in-java