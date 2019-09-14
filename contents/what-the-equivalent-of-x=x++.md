## x=x++最终x等于多少？
### 问题描述：
```
public class Tests {
    public static void main(String[] args) throws Exception {
        int x = 0;
        while(x<3) {
            x = x++;
            System.out.println(x);
        }
    }
}
```
如上，程序会进入死循环，我们知道通常对于x自增是通过x++或者x=x+1，那么为什么x=x++不是对x自增再赋值给x？

### 回答：
明确x=x++处理流程，相当于：
```java
int temp=x;
x++;
x=temp;
```
#### 解释：
考虑如下类：
```java
class test {
    public static void main(String[] args) {
        int i=0;
        i=i++;
    }
}
```
通过运行反汇编：
```bash
$ javap -c test
```
可以得到：
```java
Compiled from "test.java"
class test extends java.lang.Object{
test();
  Code:
   0:    aload_0
   1:    invokespecial    #1; //Method java/lang/Object."<init>":()V
   4:    return

public static void main(java.lang.String[]);
  Code:
   0:    iconst_0
   1:    istore_1
   2:    iload_1
   3:    iinc    1, 1
   6:    istore_1
   7:    return
}
```
以下来解释其中的助记符：
* iconst_0: 常量0被push入栈。

* istore_1: 栈顶元素被pop出，然后存储在索引为1的局部变量也就是x内。

* iload_1 : 索引为1的局部变量(即x)的值被push入栈，此时栈顶值为0。

* iinc 1, 1 :索引为1的量增加为1，也就是x变为1。

* istore_1 : 栈顶值被pop出，存入索引为1的局部变量中，因此x再次变为0。

以上，可以知道x=x++由于入栈出栈，x的值并未变化。

#### 对比：x=++x
```java
class test {
  public static void main(String[] args) {
      int i=0;
      i=++i;
  }
}
```
运行
```bash
$ javac test.java
$ javap -c test
```
得到：
```java
Compiled from "test.java"                                                           
class test {                                                                        
  test();                                                                           
    Code:                                                                           
       0: aload_0                                                                   
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V 
       4: return                                                                    
                                                                                    
  public static void main(java.lang.String[]);                                      
    Code:                                                                           
       0: iconst_0                                                                  
       1: istore_1                                                                  
       2: iinc          1, 1                                                        
       5: iload_1                                                                   
       6: istore_1                                                                  
       7: return                                                                    
}                                                                                   
```
可以看出是先对x进行加一，再将x的值push入栈，随后再将栈顶值赋给x。


