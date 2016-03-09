##从一个多层嵌套循环中直接跳出

###问题
Java中如何从一个多层嵌套循环中退出，例如下面，有两个循环，break只能退出一个for循环，不能直接跳过第二个for循环
```java
for (Type type : types) {  
    for (Type t : types2) {  
         if (some condition) {  
             // Do something and break...  
             break; // 这样只退出了最里的for循环  
         }  
}}  
```

###回答

可以用break+label的语法，例子如下
```java
public class Test {  
  public static void main(String[] args) {  
    outerloop:  
    for (int i=0; i < 5; i++) {  
      for (int j=0; j < 5; j++) {  
        if (i * j > 6) {  
          System.out.println("Breaking");  
          break outerloop;  
        }  
        System.out.println(i + " " + j);  
      }  
    }  
    System.out.println("Done");  
  }  
}  
```

首先在for循环前加标签，如例子中的outerloop，然后在for循环内break label(如本例的outerloop),就会跳出该label指定的for循环。

stackoverflow链接：
http://stackoverflow.com/questions/886955/breaking-out-of-nested-loops-in-java
