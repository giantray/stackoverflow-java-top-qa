##根据字符串获得枚举值

###问题
假设我有一个枚举类型
```
public enum Blah {
    A, B, C, D
}
```
我想要通过字符串来获得对应枚举类型中的值，比如我希望可以通过```"A"``` 获得到 ```Blah.A```,可以么？

```Enum.valueOf()``` 方法可以实现么？如果可以，我该怎么使用它？

###回答
是的，你可以用 ```Blah.valueOf("A")``` 来得到 ```Blah.A``` 。

```valueOf()```和```values()``` 两个静态方法是在编译时生成的，你在源码里是找不到它们的——你可以在某些JavaDoc里找到他们。

stackoverflow链接
http://stackoverflow.com/questions/604424/lookup-enum-by-string-value
