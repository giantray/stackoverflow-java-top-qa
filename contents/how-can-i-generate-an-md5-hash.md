







```java
import java.security.*;

...

byte[] bytesOfMessage = yourString.getBytes("UTF-8");
MessageDigest md = MessageDigest.getInstance("MD5");
byte[] digest = md.digest(bytesOfMessage);
```

