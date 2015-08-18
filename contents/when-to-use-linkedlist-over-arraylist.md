##LinkedList、ArrayList各自的使用场景，如何确认应该用哪一个呢？

一言以蔽之，在大部分情况下，使用ArrayList会好一些。

###耗时上各有优缺点。ArrayList稍有优势
List只是一个接口，而LinkedList、ArrayList是List的不同实现。LinkedList的模型是双向链表，而ArrayList则是动态数组

首先对比下常用操作的算法复杂度
#####LinkedList
- get(int index) : O(n)
- add(E element) : O(1)
- add(int index, E element) : O(n)
- remove(int index) : O(n)
- Iterator.remove() : O(1) <--- LinkedList<E>的主要优点
- ListIterator.add(E element) is O(1) <---  LinkedList<E>的主要优点

#####ArrayList
- get(int index) : O(1) <---  ArrayList<E>的主要优点
- add(E element) : 基本是O(1) , 因为动态扩容的关系，最差时是 O(n) 
- add(int index, E element) : 基本是O( n - index) , 因为动态扩容的关系，最差时是 O(n) 
- remove(int index) : O(n - index) (例如，移除最后一个元素，是 O(1))
- Iterator.remove() : O(n - index)
- ListIterator.add(E element) : O(n - index)
- LinkedList，因为本质是个链表，所以通过Iterator来插入和移除操作的耗时，都是个恒量，但如果要获取某个位置的元素，则要做指针遍历。因此，get操作的耗时会跟List长度有关

对于ArrayList来说，得益于快速随机访问的特性，获取任意位置元素的耗时，是常量的。但是，如果是add或者remove操作，要分两种情况，如果是在尾部做add，也就是执行add方法（没有index参数），此时不需要移动其他元素，耗时是O(1),但如果不是在尾部做add，也就是执行add(int index, E element),这时候在插入新元素的同时，也要移动该位置后面的所有元素，以为新元素腾出位置，此时耗时是O(n-index)。另外，当List长度超过初始化容量时，会自动生成一个新的array(长度是之前的1.5倍),此时会将旧的array移动到新的array上，这种情况下的耗时是O(n)。

**总之，get操作，ArrayList快一些。而add操作，两者差不多**。（除非是你希望在List中间插入节点，且维护了一个Iterator指向指定位置，这时候linkedList能快一些，但是，我们更多时候是直接在尾部插入节点，这种特例的情况并不多）

###空间占用上，ArrayList完胜
看下两者的内存占用图
![](http://img.blog.csdn.net/20141017095352885?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6ZXlhbmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这三个图，横轴是list长度，纵轴是内存占用值。**两条蓝线是LinkedList，两条红线是ArrayList**

可以看到，LinkedList的空间占用，要远超ArrayList。LinkedList的线更陡，随着List长度的扩大，所占用的空间要比同长度的ArrayList大得多。
注：从mid JDK6之后，默认启用了CompressedOops ，因此64位及32位下的结果没有差异，LinkedList x64和LinkedList x32的线是一样的。

stackoverflow原址：
http://stackoverflow.com/questions/322715/when-to-use-linkedlist-over-arraylist
