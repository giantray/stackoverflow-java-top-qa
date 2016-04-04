# 为什么打印“B”会明显的比打印“#”慢

## 问题

我生成了两个`1000`x`1000`的矩阵：

第一个矩阵：`O`和`#`。  
第二个矩阵：`O`和`B`。

使用如下的代码，生成第一个矩阵需要8.52秒:

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


而使用这段代码，生成第二个矩阵花费了259.152秒:

    Random r = new Random();
    for (int i = 0; i < 1000; i++) {
        for (int j = 0; j < 1000; j++) {
            if(r.nextInt(4) == 0) {
                System.out.print("O");
            } else {
                System.out.print("B"); //only line changed
            }
        }
                    
        System.out.println("");
    }

如此大的运行时间差异的背后究竟是什么原因呢？

---

正如评论中所建议的，只打印`System.out.print("#");`用时7.8871秒，而`System.out.print("B");`则给出`still printing...`。

另外有人指出这段代码对他们来说是正常的, 我使用了[Ideone.com](http://ideone.com)，这两段代码的执行速度是相同的。

测试条件：

 - 我在Netbeans 7.2中运行测试，由控制台显示输出
 - 我使用了`System.nanoTime()`来计算时间
 
 ## 解答
 
 *纯粹的推测*是因为你使用的终端尝试使用[单词换行][1]而不是字符换行，并且它认为`B`是一个单词而`#`却不是。所以当它到达行尾并且寻找一个换行的地方的时候，如果是`#`就可以马上换行；而如果是`B`，它则需要花更长时间搜索，因为可能会有更多的内容才能换行（在某些终端会非常费时，比如说它会先输出退格再输出空格去覆盖被换行的那部分字符）。

但这都只是纯粹的推测。


  [1]: http://en.wikipedia.org/wiki/Word_wrap
  
  
stackoverflow原址：http://stackoverflow.com/questions/21947452/why-is-printing-b-dramatically-slower-than-printing
