# 如何产生一个随机的字母数字串作为 session 的唯一标识符?

如果允许产生的随机字符串是可猜测的(随机字符串比较都短,或者使用有缺陷的随机数生成器),进而导致攻击者可能会劫持到会话的,可以使用一个相对简单随机数生成代码,如下所示:
```
public class RandomString {

    private static final char[] symbols;

    static {
        StringBuilder tmp = new StringBuilder();
        for (char ch = '0'; ch <= '9'; ++ch)
            tmp.append(ch);
        for (char ch = 'a'; ch <= 'z'; ++ch)
            tmp.append(ch);
        symbols = tmp.toString().toCharArray();
    }

    private final Random random = new Random();

    private final char[] buf;

    public RandomString(int length) {
        if (length < 1)
            throw new IllegalArgumentException("length < 1: " + length);
        buf = new char[length];
    }

    public String nextString() {
        for (int idx = 0; idx < buf.length; ++idx)
            buf[idx] = symbols[random.nextInt(symbols.length)];
        return new String(buf);
    }
}
```

为了安全,可以考虑使用下面这段简洁且安全的代码,不过用其作为 session 的标识符,倒显得有点大材小用了（比较耗时）:
```
import java.security.SecureRandom;

public final class SessionIdentifierGenerator {
  private SecureRandom random = new SecureRandom();

  public String nextSessionId() {
    return new BigInteger(130, random).toString(32);
  }
}
```

其工作原理就是，使用一个 130 位的安全的随机数生成器生成一个随机数，接着转化为 32 进制。我们知道，128 位安全随机数的生成已经是足够安全的，不过以 32 进制编码的每一个数字可编码 5 位，所以需要取大于 128 且是 5 的倍数，所以就选择了 130 位。相对于 随机 UUID 来说(在标准输出中，每个字符使用 3.4 bit，共 122 bit），每个字符使用 5 个随机的 bit 来编码的方式，显得更为简洁和高效。

译者注：上面两段代码，生成26位随机字符串，第一段代码每次耗时不到1ms，第二段耗时约100ms。也就是说第一段代码更快，但第二段代码更安全，但更耗时。

stackoverflow原链接：
http://stackoverflow.com/questions/41107/how-to-generate-a-random-alpha-numeric-string