## 如何在整数左填充0

### 问题
如何在整数左填充0
举例 1 = "0001"


### 答案一，String.format

    String.format("%05d", yournumber);

用0填充，总长度为5
https://docs.oracle.com/javase/8/docs/api/java/util/Formatter.html

### 答案二，ApacheCommonsLanguage
如果需要在Java 1.5前使用，可以利用 Apache Commons Language 方法

    org.apache.commons.lang.StringUtils.leftPad(String str, int size, '0')

### 答案三，DecimalFormat
    import java.text.DecimalFormat;
    class TestingAndQualityAssuranceDepartment
    {
        public static void main(String [] args)
        {
            int x=1;
            DecimalFormat df = new DecimalFormat("00");
            System.out.println(df.format(x));
        }
    }

### 答案四，自己实现
如果效率很重要的话，相比于 String.format 函数的可以自己实现

    /**
     * @param in The integer value
     * @param fill The number of digits to fill
     * @return The given value left padded with the given number of digits
     */
    public static String lPadZero(int in, int fill){

        boolean negative = false;
        int value, len = 0;

        if(in >= 0){
            value = in;
        } else {
            negative = true;
            value = - in;
            in = - in;
            len ++;
        }

        if(value == 0){
            len = 1;
        } else{         
            for(; value != 0; len ++){
                value /= 10;
            }
        }

        StringBuilder sb = new StringBuilder();

        if(negative){
            sb.append('-');
        }

        for(int i = fill; i > len; i--){
            sb.append('0');
        }

        sb.append(in);

        return sb.toString();       
    }

 效率对比

    public static void main(String[] args) {
        Random rdm;
        long start;

        // Using own function
        rdm = new Random(0);
        start = System.nanoTime();

        for(int i = 10000000; i != 0; i--){
            lPadZero(rdm.nextInt(20000) - 10000, 4);
        }
        System.out.println("Own function: " + ((System.nanoTime() - start) / 1000000) + "ms");

        // Using String.format
        rdm = new Random(0);        
        start = System.nanoTime();

        for(int i = 10000000; i != 0; i--){
            String.format("%04d", rdm.nextInt(20000) - 10000);
        }
        System.out.println("String.format: " + ((System.nanoTime() - start) / 1000000) + "ms");
    }

  结果
  自己的实现：1697ms
  String.format：38134ms

### 答案，Google Guava
Maven：

    <dependency>
         <artifactId>guava</artifactId>
         <groupId>com.google.guava</groupId>
         <version>14.0.1</version>
    </dependency>
样例：

    Strings.padStart("7", 3, '0') returns "007"
    Strings.padStart("2020", 3, '0') returns "2020"
注意：
Guava 是非常有用的库，它提供了很多有用的功能，包括了Collections, Caches, Functional idioms, Concurrency, Strings, Primitives, Ranges, IO, Hashing, EventBus等


stackoverflow原址：
http://stackoverflow.com/questions/473282/how-can-i-pad-an-integers-with-zeros-on-the-left