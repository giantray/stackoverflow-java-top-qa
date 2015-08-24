##finally语句是否一定会执行
###问题
在try/catch块中我加了return语句。那么finally语句是否会调用呢？
代码例子
```java
try {  
    something();  
    return success;  
}  
catch (Exception e) {   
    return failure;  
}  
finally {  
    System.out.println("i don't know if this will get printed out.");
}
```
我知道我可以直接运行看看，但因为我google了一番后，并未发现相关答案，所以我提出了这个问题，大家可以展开讨论

###回答
finally语句会被调用，除了以下情况：
1. 你调用了`System.exit()`
2. 另外一个线程通过[`interrupt`](https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html#interrupt--)中断了当前线程
3. JVM发生crash


//译注：第二点有待验证，评论区有人质疑