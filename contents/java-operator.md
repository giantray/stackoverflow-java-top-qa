##Java += 操作符实质

###问题
直到今天，我都一直以为：
i += j 等同于 i = i + j;
但假设有：
int i = 5;
long j = 8;
这时 i = i + j不能编译，但i += j却可以编译。这说明两者还是有差别的
这是否意味着，i += j,实际是等同于 i= (type of i) (i + j)呢？

###回答
这个问题，其实官方文档中已经解答了。 请看这里。[§15.26.2 Compound Assignment Operators](http://docs.oracle.com/javase/specs/jls/se5.0/html/expressions.html#15.26.2 )
再照搬下官方文档的说明
对复合赋值表达式来说，E1 op= E2(诸如i += j;i-=j等等),其实是等同于E1 = (T)((E1) op (E2)),其中，T是E1这个元素的类型。
举例来说，如下的代码
short x = 3;
x += 4.6;
等于
short x = 3;
x = (short)(x + 4.6);


stackoverflow链接
http://stackoverflow.com/questions/8710619/java-operator