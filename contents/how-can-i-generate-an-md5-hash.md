##如何计算MD5值

###问题
Java中有没有方法可以计算一个String的MD5值？


###回答
你可以用 ```MessageDigest``` 的MD5实例来计算String的MD5值。

使用 ```MessageDigest``` 和 String 时，一定要显式声明你的数据编码类型。如果你使用无参的 ```String.getBytes()``` , 它会以当前平台的默认编码来转换数据。不同平台的默认编码可能是不同的，这可能会导致你的数据不一致。

``` java
import java.security.*;

..

byte[] bytesOfMessage = yourString.getBytes("UTF-8");
MessageDigest md = MessageDigest.getInstance("MD5");
byte[] thedigest = md.digest(bytesOfMessage);
```

如果你的要计算的数据量很大，你可以循环使用 ```.update(byte[])``` 方法来加载数据。加载完毕后用 ```.digest()``` 方法来得到计算出的MD5值。

stackoverflow链接
http://stackoverflow.com/questions/415953/how-can-i-generate-an-md5-hash
