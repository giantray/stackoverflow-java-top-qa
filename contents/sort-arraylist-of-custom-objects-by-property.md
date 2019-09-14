

##通过对象属性对常规对象的ArrayList进行排序

###问题
我读过使用Comparator对常规类的ArrayList进行排序的示例，但是它们大多数使用comparedTo(),据我了解这是一个对字符串进行操作的方法。
我想要对一个由常规对象构成的ArrayList，通过它的属性(一个Date对象,getStartDate())对ArrayList进行排序。通常情况下我这样比较它们：
```java
item1.getStartDate().before(item2.getStartDate())
```
所以我能不能像下面一样：
```java
public class CustomComparator {
    public boolean compare(Object object1, Object object2) {
        return object1.getStartDate().before(object2.getStartDate());
    }
}

public class RandomName {
    ...
    Collections.sort(Database.arrayList, new CustomComparator);
    ...
}
```

###回答
 以前Date声明了Comparable,它有一个像处理字符串操作那样的compareTo方法。因此你可以这样：
```java
public class CustomComparator implements Comparator<MyObject> {
    @Override
    public int compare(MyObject o1, MyObject o2) {
        return o1.getStartDate().compareTo(o2.getStartDate());
    }
}
```
这儿的compare()方法必须返回int，所以不能像你预期那样直接返回boolean.
你的代码看起来应该是这样：
```java
Collections.sort(Database.arrayList, new CustomComparator());
```
如果你不需要重复使用comparetor的话，一种简单的方法是，把它写成一个内部类的样子：
```java
Collections.sort(Database.arrayList, new Comparator<MyObject>() {
    @Override
    public int compare(MyObject o1, MyObject o2) {
        return o1.getStartDate().compareTo(o2.getStartDate());
    }
});
```
自java8 开始，你可以使用lambda表达式一种简洁的方式来写Comparator()
```java
Collections.sort(Database.arrayList, 
                        (o1, o2) -> o1.getStartDate().compareTo(o2.getStartDate()));
```
并且List有一个sort(Comparator)方法，所以你可以进一步简化它
```java

Database.arrayList.sort((o1, o2) -> o1.getStartDate().compareTo(o2.getStartDate()));
```
这是一种司空见惯的方法，使用Comparable为一个类创建一个Comprator
```java
Database.arrayList.sort(Comparator.comparing(MyObject::getStartDate));
All of these are equivalent forms.
```

stackoverflow原地址：http://stackoverflow.com/questions/2784514/sort-arraylist-of-custom-objects-by-property

 
 






