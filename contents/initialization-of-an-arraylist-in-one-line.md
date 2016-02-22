如何用一句代码初始化一个ArrayList
##问题描述

我想创建一个用于测试的的选项列表，首先：
```
ArrayList<String> places = new ArrayList<String>();
places.add("Buenos Aires");
places.add("Córdoba");
places.add("La Plata");
```
之后我如下重构了代码：
```
ArrayList<String> places = new ArrayList<String>(
    Arrays.asList("Buenos Aires", "Córdoba", "La Plata"));
```
是否还有更好的方法来完成这个功能呢？

###回答一（750赞）
实际上，可能初始化ArrayList的“最好”的方法就如你所写的一样，无论如何，都不需要创建一个新的list。
```
ArrayList<String> list = new ArrayList<String>();
list.add("A");
list.add("B");
list.add("C");
```
但问题是，有相当多多的类型需要参考`list`实例。
可以有替代的办法，比如使用匿名内部类，初始化实例，也被称为一个“double brace initailzation"。
```
ArrayList<String> list = new ArrayList<String>() {{
    add("A");
    add("B");
    add("C");
}};
```
然而，我不是太喜欢这种方法，因为这样得到是一个初始化一个实例的ArrayList的子类，并且这个类只是被用来创建一个对象，好像我有点过分了。
如果 `the Collection Literals proposal for Project Coin （抱歉，不知怎么翻译）`被接受的话，可能是最好的方式（将被引入Java7,但它不太可能成为Java 8的一部分）。
[更多关于the Collection Literals proposal的内容链接](http://stackoverflow.com/questions/924285/efficiency-of-java-double-brace-initialization)
```
List<String> list = ["A","B","C"];
```
不行的是，并没有帮到你。这会初始化成一个不可变的列表，而不是一个ArrayList，而且，现在还不能用。

###回答二（714赞）
如果你只是作为一个List声明的话，这样可能会更简单.（必须是ArrayList吗）
```
List<String> places = Arrays.asList("Buenos Aires", "Córdoba", "La Plata");
```
或者只有一个元素：
```
List<String> places = Collections.singletonList("Buenos Aires");
```
这意味着places是不可变的(如果改变它，将会抛出一个异常).
让一个可变列表是一个具体的ArrayList，您可以从不可变列表创建一个ArrayList:
```
ArrayList<String> places = new ArrayList<>(Arrays.asList("Buenos Aires", "Córdoba", "La Plata"));
```
[原文链接：Initialization of an ArrayList in one line](http://stackoverflow.com/questions/1005073/initialization-of-an-arraylist-in-one-line)

