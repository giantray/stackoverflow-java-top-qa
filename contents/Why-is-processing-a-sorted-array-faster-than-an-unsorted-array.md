## 为什么处理排序的数组要比非排序的快
### 问题
以下是**c++**的一段非常神奇的代码。由于一些奇怪原因，对数据排序后奇迹般的让这段代码快了近6倍！！
```
#include <algorithm>
#include <ctime>
#include <iostream>

int main()
{
    // Generate data
    const unsigned arraySize = 32768;
    int data[arraySize];

    for (unsigned c = 0; c < arraySize; ++c)
        data[c] = std::rand() % 256;

    // !!! With this, the next loop runs faster
    std::sort(data, data + arraySize);

    // Test
    clock_t start = clock();
    long long sum = 0;

    for (unsigned i = 0; i < 100000; ++i)
    {
        // Primary loop
        for (unsigned c = 0; c < arraySize; ++c)
        {
            if (data[c] >= 128)
                sum += data[c];
        }
    }

    double elapsedTime = static_cast<double>(clock() - start) / CLOCKS_PER_SEC;

    std::cout << elapsedTime << std::endl;
    std::cout << "sum = " << sum << std::endl;
}
```


- 没有`std::sort(data, data + arraySize);`,这段代码运行了11.54秒.
- 有这个排序的代码，则运行了1.93秒.
我原以为这也许只是语言或者编译器的不一样的问题，所以我又用Java试了一下。

以下是Java代码段
```
import java.util.Arrays;
import java.util.Random;

public class Main
{
    public static void main(String[] args)
    {
        // Generate data
        int arraySize = 32768;
        int data[] = new int[arraySize];

        Random rnd = new Random(0);
        for (int c = 0; c < arraySize; ++c)
            data[c] = rnd.nextInt() % 256;

        // !!! With this, the next loop runs faster
        Arrays.sort(data);

        // Test
        long start = System.nanoTime();
        long sum = 0;

        for (int i = 0; i < 100000; ++i)
        {
            // Primary loop
            for (int c = 0; c < arraySize; ++c)
            {
                if (data[c] >= 128)
                    sum += data[c];
            }
        }

        System.out.println((System.nanoTime() - start) / 1000000000.0);
        System.out.println("sum = " + sum);
    }
}
```

结果相似，没有很大的差别。

----------------------------------------------------------
我首先得想法是排序把数据放到了cache中，但是我下一个想法是我之前的想法是多么傻啊，因为这个数组刚刚被构造。
- 到底这是为什么呢？
- 为什么排序的数组会快于没有排序的数组？
- 这段代码是为了求一些无关联的数据的和，排不排序应该没有关系啊。

### 回答
#### 什么是分支预测？
看看这个铁路分岔口
![](http://i.stack.imgur.com/muxnt.jpg)
Image by Mecanismo, via Wikimedia Commons. Used under the CC-By-SA 3.0 license.

为了理解这个问题，想象一下，如果我们回到19世纪.

你是在分岔口的操作员。当你听到列车来了，你没办法知道这两条路哪一条是正确的。然后呢，你让列车停下来，问列车员哪条路是对的，然后你才转换铁路方向。

**火车很重有很大的惯性。所以他们得花费很长的时间开车和减速。**

是不是有个更好的办法呢？你猜测哪个是火车正确的行驶方向
- 如果你猜对了，火车继续前行
- 如果你猜错了，火车得停下来，返回去，然后你再换条路。

**如果你每次都猜对了，那么火车永远不会停下来。**
**如果你猜错太多次，那么火车会花费很多时间来停车，返回，然后再启动**

----------------------------------------------------------
**考虑一个if条件语句**：在处理器层面上，这是一个分支指令：
![](http://i.stack.imgur.com/pyfwC.png)
当处理器看到这个分支时，没办法知道哪个将是下一条指令。该怎么办呢？貌似只能暂停执行，直到前面的指令完成，然后再继续执行正确的下一条指令？
现代处理器很复杂，因此它需要很长的时间"热身"、"冷却"

是不是有个更好的办法呢？你猜测下一个指令在哪！
- 如果你猜对了，你继续执行。
- 如果你猜错了，你需要flush the pipeline，返回到那个出错的分支，然后你才能继续。

**如果你每次都猜对了**，那么你永远不会停
**如果你猜错了太多次**，你就要花很多时间来滚回，重启。

-------------------------------------------------------
这就是分支预测。我承认这不是一个好的类比，因为火车可以用旗帜来作为方向的标识。但是在电脑中，处理器不能知道哪一个分支将走到最后。

所以怎样能很好的预测，尽可能地使火车必须返回的次数变小？你看看火车之前的选择过程，如果这个火车往左的概率是99%。那么你猜左，反之亦然。如果每3次会有1次走这条路，那么你也按这个三分之一的规律进行。

**换句话说，你试着定下一个模式，然后按照这个模式去执行**。这就差不多是分支预测是怎么工作的。

大多数的应用都有很好的分支预测。所以现代的分支预测器通常能实现大于90%的命中率。但是当面对没有模式识别、无法预测的分支，那分支预测基本就没用了。

如果你想知道更多:[Branch predictor" article on Wikipedia](https://en.wikipedia.org/wiki/Branch_predictor).

-----------------------------------------------------

#### 有了前面的说明，问题的来源就是这个if条件判断语句
```
if (data[c] >= 128)
    sum += data[c];
```
注意到数据是分布在0到255之间的。当数据排好序后，基本上前一半大的的数据不会进入这个条件语句，而后一半的数据，会进入该条件语句.

连续的进入同一个执行分支很多次，这对分支预测是非常友好的。可以更准确地预测，从而带来更高的执行效率。

##### 快速理解一下
```
T = branch taken
N = branch not taken

data[] = 0, 1, 2, 3, 4, ... 126, 127, 128, 129, 130, ... 250, 251, 252, ...
branch = N  N  N  N  N  ...   N    N    T    T    T  ...   T    T    T  ...

       = NNNNNNNNNNNN ... NNNNNNNTTTTTTTTT ... TTTTTTTTTT  (easy to predict)
```
但是当数据是完全随机的，分支预测就没什么用了。因为他无法预测随机的数据。因此就会有大概50%的概率预测出错。
```
data[] = 226, 185, 125, 158, 198, 144, 217, 79, 202, 118,  14, 150, 177, 182, 133, ...
branch =   T,   T,   N,   T,   T,   T,   T,  N,   T,   N,   N,   T,   T,   T,   N  ...

       = TTNTTTTNTNNTTTN ...   (completely random - hard to predict)
```
----------------------------------------------------------

##### 我们能做些什么呢
如果编译器无法优化带条件的分支，如果你愿意牺牲代码的可读性换来更好的性能的话，你可以用下面的一些技巧。

把
```
if (data[c] >= 128)
    sum += data[c];
```
替换成
```
int t = (data[c] - 128) >> 31;
sum += ~t & data[c];
```
这消灭了分支，把它替换成按位操作.

（说明：这个技巧不是非常严格的等同于原来的if条件语句。但是在`data[]`当前这些值下是OK的）

**使用的设备参数是：Core i7 920 @ 3.5 GHz**
C++ - Visual Studio 2010 - x64 Release
```
//  Branch - Random
seconds = 11.777

//  Branch - Sorted
seconds = 2.352

//  Branchless - Random
seconds = 2.564

//  Branchless - Sorted
seconds = 2.587
```
Java - Netbeans 7.1.1 JDK 7 - x64
```
//  Branch - Random
seconds = 10.93293813

//  Branch - Sorted
seconds = 5.643797077

//  Branchless - Random
seconds = 3.113581453

//  Branchless - Sorted
seconds = 3.186068823
```
结论：
- 用了分支(if)：没有排序和排序的数据，效率有很大的区别
- 用了上面提到的按位操作替换：排序与否，效率没有很大的区别
- 在使用C++的情况下，按位操作还是要比排好序的分支操作要慢。

一般的建议是尽量避免在关键循环上出现对数据很依赖的分支。（就像这个例子）

------------------------------------------------

更新：

- GCC 4.6.1 用了 `-O3` or `-ftree-vectorize`，在64位机器上，数据有没有排序，都是一样快。
 **...**
**...**
**...等各种例子**


说明了现代编译器越发成熟强大，可以在这方面充分优化代码的执行效率

### 相关内容

CPU的流水线指令执行
   
 想象现在有一堆指令等待CPU去执行，那么CPU是如何执行的呢？具体的细节可以找一本计算机组成原理来看。CPU执行一堆指令时，并不是单纯地一条一条取出来执行，而是按照一种流水线的方式，在CPU真正指令前，这条指令就像工厂里流水线生产的产品一样，已经被经过一些处理。简单来说，一条指令可能经过过程：取指(Fetch)、解码(Decode)、执行(Execute)、放回(Write-back)。 

假设现在有指令序列ABCDEFG。当CPU正在执行(execute)指令A时，CPU的其他处理单元（CPU是由若干部件构成的）其实已经预先处理到了指令A后面的指令，例如B可能已经被解码，C已经被取指。这就是流水线执行，这可以保证CPU高效地执行指令。 

分支预测

如上所说，CPU在执行一堆顺序执行的指令时，因为对于执行指令的部件来说，其基本不需要等待，因为诸如取指、解码这些过程早就被做了。但是，当CPU面临非顺序执行的指令序列时，例如之前提到的跳转指令，情况会怎样呢？ 

取指、解码这些CPU单元并不知道程序流程会跳转，只有当CPU执行到跳转指令本身时，才知道该不该跳转。所以，取指解码这些单元就会继续取跳转指令之后的指令。当CPU执行到跳转指令时，如果真的发生了跳转，那么之前的预处理（取指、解码）就白做了。这个时候，CPU得从跳转目标处临时取指、解码，然后才开始执行，这意味着：CPU停了若干个时钟周期！ 

这其实是个问题，如果CPU的设计放任这个问题，那么其速度就很难提升起来。为此，人们发明了一种技术，称为branch prediction，也就是分支预测。分支预测的作用，就是预测某个跳转指令是否会跳转。而CPU就根据自己的预测到目标地址取指令。这样，即可从一定程度提高运行速度。当然，分支预测在实现上有很多方法。 


**stackoverflow链接**：

这个问题的所有回答中，最高的回答，获取了上万个vote，还有很多个回答，非常疯狂，大家觉得不过瘾可以移步到这里查看

http://stackoverflow.com/questions/11227809/why-is-processing-a-sorted-array-faster-than-an-unsorted-array
