##为什么打印B要比打印#慢很多？

###问题  
我生成了两个大小为 1000 * 1000 的矩阵  
第一个矩阵：O和#  
第二个矩阵：O和B  
使用以下代码，第一个矩阵仅用时8.25s就完成了：  
```java
Random r = new Random();
for (int i = 0; i < 1000; i++) {
    for (int j = 0; j < 1000; j++) {
        if(r.nextInt(4) == 0) {
            System.out.print("O");
        } else {
            System.out.print("#");
        }
    }

   System.out.println("");
 }
````
而使用相同的代码时，第二个矩阵却执行了259.152s  
````java
Random r = new Random();
for (int i = 0; i < 1000; i++) {
    for (int j = 0; j < 1000; j++) {
        if(r.nextInt(4) == 0) {
            System.out.print("O");
        } else {
            System.out.print("B");
        }
    }

   System.out.println("");
 }
````
为什么两者的执行时间会有如此巨大的差距？   
- - -
评论中一些人建议仅执行`System.out.print("#");`以及`System.out.print("B");`前者用时7.8871s，而后者仍然在打印（即花费时间过多--译者注）   
另外有些人指出在他们那里工作正常（即两者花费时间差不错--译者注），我在[Ideone.com](http://ideone.com/)环境中运行两段代码，执行时间也差不多。   

测试条件：   
- Netbeans 7.2，结果输出在IDE的控制台   
- 使用`System.nanoTime()`度量时间   

###回答   
纯推测：你正在使用的终端试图进行[“自动换行”(word-wrapping)](http://en.wikipedia.org/wiki/Word_wrap)，而不是“字符换行”(character-wrapping)，并且将'B'视为一个单词字符(word character)，而'#'视为一个非单词字符(non-word character)。因此当输出到达行尾时，控制台搜索一个位置用来换行，当遇到'#'时可以立即执行换行；然而遇到'B'时，控制台必须继续搜索，并且可能有更多的字符需要换行（这个操作在一些控制台上可能花销很大，例如，输出退格，然后输出空白字符来覆盖那些需要被换行的字符）。   
但是，这仅仅是理论推测。   

**译者注：**
对于"word-wrapping"和"character-wrapping"，我的理解是，它们的区别在于换行时是否在一个单词内部分割，例如在 charac-ter 中的-处换行，"word-wrapping"会将character全部移到下一行，而"character-wrapping"则将ter移到下一行，而charac依旧在原来的位置。   
**word-wrapping**
```
*******
character
```
**character-wrapping**
```
*******charac   
ter
```