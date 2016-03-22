## 为什么在java中存放密码更倾向于char[]而不是String

### 问题

在Swing中，password字段有一个getPassword()方法（返回char[]），而不是通常的getText()方法(返回String字符串)。同样的，我看到一个建议说不要使用字符串处理密码。
为什么在涉及passwords时，都说字符串会对安全构成威胁？感觉使用char[]不是那么的方便。

### 回答
String是不可变的。虽然String加载密码之后可以把这个变量扔掉，但是字符串并不会马上被GC回收，一但进程在GC执行到这个字符串之前被dump，dump出的的转储中就会含有这个明文的字符串。那如果我去“修改”这个字符串，比如把它赋一个新值，那么是不是就没有这个问题了？答案是否定的，因为String本身是不可修改的，任何基于String的修改函数都是返回一个新的字符串，原有的还会在内存里。

然而对于数组，你可以在抛弃它之前直接修改掉它里面的内容或者置为乱码，密码就不会存在了。但是如果你什么也不做直接交给gc的话，也会存在上面一样的问题。

所以，这是一个安全性的问题--但是，即使使用char[]也仅仅是降低了攻击者攻击的机会，而且仅仅对这种特定的攻击有效。


**stackoverflow链接**：
http://stackoverflow.com/questions/8881291/why-is-char-preferred-over-string-for-passwords-in-java

**知乎上也有相关讨论**：
https://www.zhihu.com/question/36734157
