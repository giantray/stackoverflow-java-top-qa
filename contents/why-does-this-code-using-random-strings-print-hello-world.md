##为什么以下用随机生成的文字会得出 “hello world”?

###问题
为什么以下用随机生成的文字会得出"hello world".
有人能解释一下吗?

```
System.out.println(randomString(-229985452) + " " + randomString(-147909649));

public static String randomString(int i)
{
   Random ran = new Random(i);
   StringBuilder sb = new StringBuilder();
   while (true)
   {
       int k = ran.nextInt(27);
        if (k == 0)
           break;
           
       sb.append((char)('`' + k));
   }
   return sb.toString();
}
```
###回答1（最佳）
在JAVA 里面，随机类的实现不是真正的随机,是伪随机.
就是说如果随机类的种子是一样的话，他们会生成同一组的数字。

比如说这个问题:

    new Random(-229985452).nextInt(27)

首6个生成的数字一定是:

    8
    5
    12
    12
    15
    0


而 `new Random(-147909649).nextInt(27)` 首6个生成的数字一定是:

    23
    15
    18
    12
    4
    0

而把每一个数目字加 <code>`</code> (which is 96)，就会得到了相应的英文字母:

    8  + 96 = 104 --> h
    5  + 96 = 101 --> e
    12 + 96 = 108 --> l
    12 + 96 = 108 --> l
    15 + 96 = 111 --> o
    
    23 + 96 = 119 --> w
    15 + 96 = 111 --> o
    18 + 96 = 114 --> r
    12 + 96 = 108 --> l
    4  + 96 = 100 --> d

stackoverflow链接：http://stackoverflow.com/questions/15182496/why-does-this-code-using-random-strings-print-hello-world