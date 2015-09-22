##问题

<<<<<<< HEAD
有一个 try/catch 代码块，其中包含一个打印语句。finally代码块总会被调用么？

示例：
=======
有一个 try/catch 代码块，其中包含一个打印语句。finally代码块总会被执行么？

示例：

>>>>>>> 28e7878b6828743ba520b991713ff3e6a1fc0c22
``` java
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

##回答

1. ```finally``` 将会被调用。
<<<<<<< HEAD
只有以下情况 ```finally``` 不会被调用：
=======

   只有以下情况 ```finally``` 不会被调用：
>>>>>>> 28e7878b6828743ba520b991713ff3e6a1fc0c22

 - 当你使用 ```System.exit()``` 后
 - 其他线程干扰了现在运行的线程（通过 ```interrupt``` 方法）
 - 如果 JVM 已经“撞毁”了

<<<<<<< HEAD
 Answered by [Jodonnell](http://stackoverflow.com/users/4223/jodonnell), edited by [jpaugh](http://stackoverflow.com/users/712526/jpaugh).

2. //示例代码
=======
 Answered by [Jodonnell](http://stackoverflow.com/users/4223/jodonnell),edited by [jpaugh](http://stackoverflow.com/users/712526/jpaugh).

2. //示例代码

>>>>>>> 28e7878b6828743ba520b991713ff3e6a1fc0c22
``` java
class Test
{
    public static void main(String args[]) 
    { 
    	System.out.println(Test.test()); 
    }

    public static int test()
    {
    	try {  
            	return 0;  
    	}  
    	finally {  
    	    System.out.println("finally trumps return.");
    	}
    }
}
```
<<<<<<< HEAD
输出：
=======
   输出：

>>>>>>> 28e7878b6828743ba520b991713ff3e6a1fc0c22
``` java
finally trumps return. 
0
```
<<<<<<< HEAD
Answered by [Kevin](http://stackoverflow.com/users/1058366/kevin)
=======

   Answered by [Kevin](http://stackoverflow.com/users/1058366/kevin)
>>>>>>> 28e7878b6828743ba520b991713ff3e6a1fc0c22

---
原文链接：http://stackoverflow.com/questions/65035/does-finally-always-execute-in-java?page=1&tab=votes#tab-top