如何用一行代码初始化一个ArrayList

### 问题
为了测试，我需要临时快速创建一个list。一开始我这样做：
```java
ArrayList<String> places = new ArrayList<String>();
places.add("Buenos Aires");
places.add("Córdoba");
places.add("La Plata");
```
之后我重构了下
```java
ArrayList<String> places = new ArrayList<String>(
    Arrays.asList("Buenos Aires", "Córdoba", "La Plata"));
```
是否有更加简便的方法呢？

### 回答

#### 常见方式
实际上，也许“最好”的方式，就是你写的这个方式，因为它不用再创建新的`List`:
```
ArrayList<String> list = new ArrayList<String>();
list.add("A");
list.add("B");
list.add("C");
```
只是这个方式看上去要多写些代码，让人郁闷

#### 匿名内部类
当然，还有其他方式，例如,写一个匿名内部类，然后在其中做初始化（也被称为 brace initialization）：
```
ArrayList<String> list = new ArrayList<String>() {{
    add("A");
    add("B");
    add("C");
}};
```
但是，我不喜欢这个方式。只是为了做个初始化，却要在`ArrayList`的同一行后面加这么一坨代码。

#### Arrays.asList
```
List<String> places = Arrays.asList("Buenos Aires", "Córdoba", "La Plata");
```
#### Collections.singletonList
```
List<String> places = Collections.singletonList("Buenos Aires");
```
注意：后面的这两种方式，得到的是一个定长的`List`(如果add操作会抛异常）。如果你需要一个不定长的`List`,可以这样做：
```
ArrayList<String> places = new ArrayList<>(Arrays.asList("Buenos Aires", "Córdoba", "La Plata"));

```

stackoverflow链接：
http://stackoverflow.com/questions/1005073/initialization-of-an-arraylist-in-one-line

