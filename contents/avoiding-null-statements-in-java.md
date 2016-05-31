##去掉烦人的“!=null"(判空语句)

###问题
为了避免空指针调用，我们经常会看到这样的语句
```java
if (someobject != null) {
    someobject.doCalc();
}
```
最终，项目中会存在大量判空代码，多么丑陋繁冗！如何避免这种情况？我们是否滥用了判空呢？

###回答

这是初、中级程序猿经常会遇到的问题。他们总喜欢在方法中返回null，因此，在调用这些方法时，也不得不去判空。另外，也许受此习惯影响，他们总潜意识地认为，所有的返回都是不可信任的，为了保护自己程序，就加了大量的判空。

吐槽完毕，回到这个题目本身，进行判空前，请区分以下两种情况：

1. null 是一个有效有意义的返回值(Where null is a valid response in terms of the contract; and)
2. null是无效有误的(Where it isn't a valid response.)

你可能还不明白这两句话的意思，不急，继续往下看，接下来将详细讨论这两种情况

####先说第2种情况
null就是一个不合理的参数，就应该明确地中断程序，往外抛错误。这种情况常见于api方法。例如你开发了一个接口，id是一个必选的参数，如果调用方没传这个参数给你，当然不行。你要感知到这个情况，告诉调用方“嘿，哥们，你传个null给我做甚"。

相对于判空语句，更好的检查方式有两个

1. assert语句，你可以把错误原因放到assert的参数中，这样不仅能保护你的程序不往下走，而且还能把错误原因返回给调用方，岂不是一举两得。（原文介绍了assert的使用，这里省略）
2. 也可以直接抛出空指针异常。上面说了，此时null是个不合理的参数，有问题就是有问题，就应该大大方方往外抛。

####第1种情况会更复杂一些。
这种情况下，null是个”看上去“合理的值，例如，我查询数据库，某个查询条件下，就是没有对应值，此时null算是表达了“空”的概念。

这里给一些实践建议：

-  假如方法的返回类型是collections，当返回结果是空时，你可以返回一个空的collections（empty list),而不要返回null.这样调用侧就能大胆地处理这个返回，例如调用侧拿到返回后，可以直接print list.size()，又无需担心空指针问题。（什么？想调用这个方法时，不记得之前实现该方法有没按照这个原则？所以说，代码习惯很重要！如果你养成习惯，都是这样写代码（返回空collections而不返回null)，你调用自己写的方法时，就能大胆地忽略判空）
-  返回类型不是collections，又怎么办呢？
那就返回一个空对象（而非null对象），下面举个“栗子”，假设有如下代码

```java
public interface Action {
  void doSomething();}

public interface Parser {
  Action findAction(String userInput);}
```

其中，Parse有一个接口FindAction，这个接口会依据用户的输入，找到并执行对应的动作。假如用户输入不对，可能就找不到对应的动作（Action），因此findAction就会返回null，接下来action调用doSomething方法时,就会出现空指针。
解决这个问题的一个方式，就是使用Null Object pattern（空对象模式）

我们来改造一下

类定义如下，这样定义findAction方法后，确保无论用户输入什么，都不会返回null对象：
```java
public class MyParser implements Parser {
  private static Action DO_NOTHING = new Action() {
    public void doSomething() { /* do nothing */ }
  };

  public Action findAction(String userInput) {
    // ...
    if ( /* we can't find any actions */ ) {
      return DO_NOTHING;
    }
  }}
```

对比下面两份调用实例
1. 冗余:  每获取一个对象，就判一次空
```java
Parser parser = ParserFactory.getParser();
if (parser == null) {
  // now what?
  // this would be an example of where null isn't (or shouldn't be) a valid response
}
Action action = parser.findAction(someInput);
if (action == null) {
  // do nothing} 
else {
  action.doSomething();}
```

2. 精简
```java
ParserFactory.getParser().findAction(someInput).doSomething();
```
因为无论什么情况，都不会返回空对象，因此通过findAction拿到action后，可以放心地调用action的方法。



####其他回答精选：
- 如果要用equal方法，请用object<不可能为空>.equal(object<可能为空>))
例如：
使用
`"bar".equals(foo) `
而不是
`foo.equals("bar") `
- Java8或者guava lib中，提供了Optional类，这是一个元素容器，通过它来封装对象，可以减少判空。不过代码量还是不少。不爽。
- 如果你想返回null，请停下来想一想，这个地方是否更应该抛出一个异常

stackoverflow链接：
http://stackoverflow.com/questions/271526/avoiding-null-statements-in-java?page=2&tab=votes#tab-top
