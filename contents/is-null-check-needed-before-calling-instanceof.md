## 在调用 instanceof 前需要进行null检查吗


### 问题：

null instanceof SomeClass 会返回 null 还是抛出 NullPointerException 异常

### 答案一
在调用 instanceof 前不要进行null检查
null instanceof SomeClass 会返回 null
在 Java Language Specification 中 http://docs.oracle.com/javase/specs/jls/se7/html/jls-15.html#jls-15.20.2

```
在运行时，如果该instanceof运算符的关系表达式（RelationExpression）不为 null，且这个引用可以被成功转型（ §15.16），不抛出ClassCastException，则结果为true；
 否则结果为false。
```

### 答案二
    public class IsInstanceOfTest {
    
        public static void main(final String[] args) {
    
            String s;
    
            s = "";
    
            System.out.println((s instanceof String));
            System.out.println(String.class.isInstance(s));
    
            s = null;
    
            System.out.println((s instanceof String));
            System.out.println(String.class.isInstance(s));
        }
    }
打印出

    true
    true
    false
    false
    
### 原文链接
http://stackoverflow.com/questions/2950319/is-null-check-needed-before-calling-instanceof
   

    