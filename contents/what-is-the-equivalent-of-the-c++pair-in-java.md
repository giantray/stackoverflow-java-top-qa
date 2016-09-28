Java里什么是与C++的Ｐair<L,R>相等的？
问题：
Java里没有Pair是不是一个好理由？那什么会和C++这个结构相等呢？似乎1.6版本提供了一些类似的（比如AbstractMap.SimpleEntry）,但这看起来很费解。

回答：
在comp.lang.java.help的一个论坛上,Hunter Gratzner对于java的Pair结构给出了一些论点。主要的论点是Pair类不能传达出两个值的关系的语义（你怎么能知道第一和第二代表什么？）
一个最好的校验是写一个非常简单的类，像Mike建议的，每一个应用上的pair类，Map.Entry都能很好的取代。
总的来说，我建议是最好是一个Position(x,y)类，一个Range（begin,end）和一个Entry（key,value）类，而不是一个不能告诉我应该怎么做的广泛Pair(first，second)类。

stackoverflow的地址：
http://stackoverflow.com/questions/156275/what-is-the-equivalent-of-the-c-pairl-r-in-java

