
##问题
大多数情况下，当我重新导入项目到eclipse的时候，我重写的方法都不能被正确格式化，导致这样的错误：
> The method must override a superclass method.

需要说明的是这是一个Android项目，不知道什么原因，方法的参数被篡改了，因此，我不得不手动的把他们改回来。
例如：
```java
list.setOnCreateContextMenuListener(new OnCreateContextMenuListener() {
    //这儿的参数名是正确的
    public void onCreateContextMenu(ContextMenu menu, View v, 
                                    ContextMenuInfo menuInfo) {                 
    }
});
```
初始化的时候被篡改成了这样：
```java
list.setOnCreateContextMenuListener(new OnCreateContextMenuListener() {
    //这儿的参数被篡改成了这样
    public void onCreateContextMenu(ContextMenu arg1, View arg2,
                                    ContextMenuInfo arg3) {
    }
});
```
奇怪的是，如果我移除我的代码并使用eclipse自动创建方法的话，它还是会是相同的参数（被篡改的）。因此，我真不知道那儿的问题，它本应该自动格式化代码的。
要是手动的去修改被篡改的参数名，那是一个非常痛苦的过程。要是有人能解释为什么会出现这样的情况以及怎样去解决它，我感激不尽。
是不是因为我格式化的这个方法，是另一个方法里面的参数而导致的这样的问题呢？

##回答
Eclipse的默认执行环境是java 1.5况且你使用了类的声明接口方法（在java 1.6中能使用@Ovrride注释，但是在java 1.5中一个方法只能重写父类的方法）

打开你的项目，然后找到preference并且设置java的编译版本为1.6，同时也确保你的eclipse是使用JRE 1.6 来执行你的程序的。

Stack Overflow原地址：http://stackoverflow.com/questions/1678122/must-override-a-superclass-method-errors-after-importing-a-project-into-eclips

