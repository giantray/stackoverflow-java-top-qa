##Java修饰符：public，protected，private，不加修饰符。有什么区别呢？


如下表所示,Y表示能访问(可见性），N表示不能访问，例如第一行的第3个Y，表示类的变量/方法如果是用public修饰，它的子类能访问这个变量/方法


 修饰符  |  类内部 |  同个包（package） |  子类 |  其他范围 
------------- | ------------- | -------------| -------------| -------------
public | Y |  Y |  Y |  Y
protected  |  Y |  Y |  Y |  N
无修饰符  |  Y |  Y |  N or Y(见说明） |  N
private  |  Y |  N |  N |  N


说明：
需要特别说明“无修饰符”这个情况，子类能否访问父类中无修饰符的变量/方法，取决于子类的位置。如果子类和父类在同一个包中，那么子类可以访问父类中的无修饰符的变量/方法，否则不行。

译注：本来觉得很简单一个问题，没想记录的，但看到答案，才发现自己以前错了。我以前一直以为无修饰符和private是一样的，如果没给变量加修饰符，java就默认为private。

stackoverflow链接：
http://stackoverflow.com/questions/215497/in-java-whats-the-difference-between-public-default-protected-and-private