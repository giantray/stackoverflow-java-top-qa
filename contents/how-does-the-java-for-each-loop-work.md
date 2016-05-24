## Java的foreach循环是如何工作的？
### 问题
````java
List<String> someList = new ArrayList<String>();
// add "monkey", "donkey", "skeleton key" to someList
for (String item : someList) {
    System.out.println(item);
}
````
如果不用for each语法，等价的循环语句是什么样的？
### 回答
````java
for(Iterator<String> i = someList.iterator(); i.hasNext(); ) {
    String item = i.next();
    System.out.println(item);
}
````
记住，如果需要在循环中使用i.remove;或者以某种方式获取实际的iterator，你不能使用for(:)语法，因为实际的Iterator很难被推断出来。
正如Denis Bueno写的那样，这种代码对任何实现了Iterable接口的对象都奏效。
此外，如果for(:)句法中右侧是一个数组而不是一个可迭代对象，那么内部代码用一个int型的计数器来防止数组越界。详见Java Language Specification:
http://docs.oracle.com/javase/specs/jls/se8/html/jls-14.html#jls-14.14.2

stackoverflow链接：http://stackoverflow.com/questions/85190/how-does-the-java-for-each-loop-work
