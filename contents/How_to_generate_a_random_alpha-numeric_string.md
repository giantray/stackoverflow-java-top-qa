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

为了安全,可以考虑使用下面这段简洁且安全的代码,不过用其作为 session 的标识符,可能略显昂贵了一点:
```
```