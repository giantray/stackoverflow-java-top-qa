##将ArrayList中的自定义对象按某属性排序

### 问题
我读到有关于利用比较器对一个ArrayList进行排序，但是在所有的例子中大家都用的是`compareTo`，按照我的研究这是个字符串的方法。

我想对一个ArrayList的自定义对象按照如下属性进行排序：一个Date类型对象(`getStartDay()`)。通常我这样比较它们:
`item1.getStartDate().before(item2.getStartDate())`，我想知道我能否这样写：

```
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


### 答案
由于[`Date`][1]类型实现了[`Comparable`][2]接口, 它`String`一样也有有 `compareTo`方法.

所以你的自定义[`Comparator`][3]可以像这样:

    public class CustomComparator implements Comparator<MyObject> {
        @Override
        public int compare(MyObject o1, MyObject o2) {
            return o1.getStartDate().compareTo(o2.getStartDate());
        }
    }

`compare()`方法必须返回`int`类型, 所以反正你也不能像你预想的那样返回一个`boolean`类型。

你的排序代码可以跟你写的基本差不多:

    Collections.sort(Database.arrayList, new CustomComparator());

稍微简短一点的实现这些的方法，如果你不需要重用你的比较器的话，是把它写成一个内联的匿名类：


    Collections.sort(Database.arrayList, new Comparator<MyObject>() {
        @Override
        public int compare(MyObject o1, MyObject o2) {
            return o1.getStartDate().compareTo(o2.getStartDate());
        }
    });

---
#从Java8起

你可以把上面的例子写成更短的形式，通过在`Comparator(比较器)`中使用[lambda表达式][4]:

    Collections.sort(Database.arrayList, 
                            (o1, o2) -> o1.getStartDate().compareTo(o2.getStartDate()));

并且`List`有[`sort(Comparator)`][5]方法, 于是你可以把这个写的更短：

    Database.arrayList.sort((o1, o2) -> o1.getStartDate().compareTo(o2.getStartDate()));

这种写法如此常用以至于有一个[内置方法][6]来为带有实现`Comparable(可比较)`接口的成员变量的类快速生成`Comparator(比较器)`

    Database.arrayList.sort(Comparator.comparing(MyObject::getStartDate));

上述几种写法都完全等价。


  [1]: //docs.oracle.com/javase/8/docs/api/java/util/Date.html
  [2]: //docs.oracle.com/javase/8/docs/api/java/lang/Comparable.html
  [3]: //docs.oracle.com/javase/8/docs/api/java/util/Comparator.html
  [4]: //docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html
  [5]: //docs.oracle.com/javase/8/docs/api/java/util/List.html#sort-java.util.Comparator-
  [6]: //docs.oracle.com/javase/8/docs/api/java/util/Comparator.html#comparing-java.util.function.Function-

stackoverflow链接：http://stackoverflow.com/questions/2784514/sort-arraylist-of-custom-objects-by-property