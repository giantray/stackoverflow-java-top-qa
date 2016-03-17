#怎样将堆栈追踪信息转换为字符串
##问题
将`Throwable.getStackTrace()`的结果转换为一个字符串来来描述堆栈信息的最简单的方法是什么


###最佳答案
可以用下面的方法将异常堆栈信息转换为字符串类型。该类在Apache commons-lang-2.2.jar中可以找到：
[`org.apache.commons.lang.exception.ExceptionUtils.getStackTrace(Throwable)`](org.apache.commons.lang.exception.ExceptionUtils.getStackTrace\(Throwable\))

###答案二
用 [`Throwable.printStackTrace(PrintWriter pw)`](https://docs.oracle.com/javase/8/docs/api/java/lang/Throwable.html#printStackTrace-java.io.PrintWriter-)可以输出堆栈信息：
````java
StringWriter sw = new StringWriter();
PrintWriter pw = new PrintWriter(sw);
t.printStackTrace(pw);
sw.toString(); // stack trace as a string
````

###答案三
````java
StringWriter sw = new StringWriter();
e.printStackTrace(new PrintWriter(sw));
String exceptionAsString = sw.toString();
````

###答案四
````java
public String stackTraceToString(Throwable e) {
    StringBuilder sb = new StringBuilder();
    for (StackTraceElement element : e.getStackTrace()) {
        sb.append(element.toString());
        sb.append("\n");
    }
    return sb.toString();
}
````

stackoverflow链接：
http://stackoverflow.com/questions/1149703/how-can-i-convert-a-stack-trace-to-a-string