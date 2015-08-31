# Java 是否支持默认的参数值？

在 c++ 中，常见到如下的方法定义(param3 默认为 false)：

```
void MyParameterizedFunction(String param1, int param2, bool param3=false);
```

那在 java 中，是否也支持这样的定义方式？

答案是否定的，不过我们可以通过多种方式处理这种参数默认值的情况。

### [创建者模式](http://en.wikipedia.org/wiki/Builder_pattern)
使用创建者模式，你可以设定部分参数是有默认值，部分参数是可选的。如：

```
Student s1 = new StudentBuilder().name("Eli").buildStudent();
Student s2 = new StudentBuilder()
                 .name("Spicoli")
                 .age(16)
                 .motto("Aloha, Mr Hand")
                 .buildStudent();
```


### 方法（构造函数）重载
如:

```
void foo(String a, Integer b) {
    //...
}

void foo(String a) {
    foo(a, 0); // here, 0 is a default value for b
}

foo("a", 2);
foo("a");
```
构造函数重载，对于参数比较少的情况下，比较适合；当参数相对多的时候，可以考虑使用静态工厂方法，或添加一个参数辅助对象。

如果是常规方法重载，可以考虑使用 参数辅助对象，或者重命名多种情况（比如说，有多个开银行卡的重载方法，可以根据需要重命名为 开交行卡，开招行卡 等多种方法）。	

### null 的传递
当有多个默认参数时，可以考虑传递 null，当参数为 null 时，将参数设为 默认值。如：

```
void foo(String a, Integer b, Integer c) {
    b = b != null ? b : 0;
    c = c != null ? c : 0;
    //...
}

foo("a", null, 2);
```

### 多参数方式
当有多个参数，且某些参数可以忽略不设置的情况下，可以考虑使用多参数方式。

- 可选的参数类型的一致

```
void foo(String a, Integer... b) {
    Integer b1 = b.length > 0 ? b[0] : 0;
    Integer b2 = b.length > 1 ? b[1] : 0;
    //...
}

foo("a");
foo("a", 1, 2);
```

- 可选参数类型不一致

```
void foo(String a, Object... b) {
    Integer b1 = 0;
    String b2 = "";
    if (b.length > 0) {
      if (!(b[0] instanceof Integer)) { 
          throw new IllegalArgumentException("...");
      }
      b1 = (Integer)b[0];
    }
    if (b.length > 1) {
        if (!(b[1] instanceof String)) { 
            throw new IllegalArgumentException("...");
        }
        b2 = (String)b[1];
        //...
    }
    //...
}

foo("a");
foo("a", 1);
foo("a", 1, "b2");
```

### 使用 Map 作为方法中的参数
当参数很多，且大部分参数都会使用默认值的情况，可以使用 Map 作为方法中的参数。

```
void foo(Map<String, Object> parameters) {
    String a = ""; 
    Integer b = 0;
    if (parameters.containsKey("a")) { 
        if (!(parameters.get("a") instanceof Integer)) { 
            throw new IllegalArgumentException("...");
        }
        a = (String)parameters.get("a");
    }
    if (parameters.containsKey("b")) { 
        //... 
    }
    //...
}

foo(ImmutableMap.<String, Object>of(
    "a", "a",
    "b", 2, 
    "d", "value")); 
```


stackoverflow原址：https://stackoverflow.com/questions/997482/does-java-support-default-parameter-values