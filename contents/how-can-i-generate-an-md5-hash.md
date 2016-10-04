##如何生成MD5哈希码？

###问题
在Java中是否有方法可以生成一个字符串的哈希码？

###回答

**被采纳答案**

可以使用`java.security.MessageDigest`。调用`getInstance("MD5")`来获得一个MD5消息摘要实例(message digest)，然后使用该实例得到MD5哈希码。

**最高票答案**

`MessageDigest`类可以为你提供一个MD5摘要实例。

在处理字符串和加密类时，一定要指定字节表示的编码方式。如果你只是使用`string.getBytes()`，那字节的编码方式与平台相关，且并不是多有的平台的默认方式都是一致的。

```java
import java.security.*;

...

byte[] bytesOfMessage = yourString.getBytes("UTF-8");
MessageDigest md = MessageDigest.getInstance("MD5");
byte[] digest = md.digest(bytesOfMessage);
```

如果你有大量的数据需要处理，可以查看`update(byte[])`方法，该方法可以被重复调用。然后再调用`digest()`方法来包含md5哈希码。