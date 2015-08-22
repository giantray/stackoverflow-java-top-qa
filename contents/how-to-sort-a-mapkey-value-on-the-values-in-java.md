##`Map<Key,Value>`基于Value值排序

###方法1：
使用TreeMap，可以参考下面的代码
```java
public class Testing {  
  
    public static void main(String[] args) {  
  
        HashMap<String,Double> map = new HashMap<String,Double>();  
        ValueComparator bvc =  new ValueComparator(map);  
        TreeMap<String,Double> sorted_map = new TreeMap<String,Double>(bvc);  
  
        map.put("A",99.5);  
        map.put("B",67.4);  
        map.put("C",67.4);  
        map.put("D",67.3);  
  
        System.out.println("unsorted map: "+map);  
  
        sorted_map.putAll(map);  
  
        System.out.println("results: "+sorted_map);  
    }  
}  
  
class ValueComparator implements Comparator<String> {  
  
    Map<String, Double> base;  
    public ValueComparator(Map<String, Double> base) {  
        this.base = base;  
    }  
  
    // Note: this comparator imposes orderings that are inconsistent with equals.      
    public int compare(String a, String b) {  
        if (base.get(a) >= base.get(b)) {  
            return -1;  
        } else {  
            return 1;  
        } // returning 0 would merge keys  
    }  
}  
```
译注：如果不自己写Comparator,treemap默认是用key来排序

###方法2：

先通过linkedlist排好序，再放到LinkedHashMap中
```java
public class MapUtil  
{  
    public static <K, V extends Comparable<? super V>> Map<K, V>   
        sortByValue( Map<K, V> map )  
    {  
        List<Map.Entry<K, V>> list =  
            new LinkedList<Map.Entry<K, V>>( map.entrySet() );  
        Collections.sort( list, new Comparator<Map.Entry<K, V>>()  
        {  
            public int compare( Map.Entry<K, V> o1, Map.Entry<K, V> o2 )  
            {  
                return (o1.getValue()).compareTo( o2.getValue() );  
            }  
        } );  
  
        Map<K, V> result = new LinkedHashMap<K, V>();  
        for (Map.Entry<K, V> entry : list)  
        {  
            result.put( entry.getKey(), entry.getValue() );  
        }  
        return result;  
    }  
}  
```
译注：这两种方法，我简单测试了下，如果map的size在十万级别以上，两者的耗时都是几百毫秒，第二个方法会快一些。否则，第一个方法快一些。因此，如果你处理的map，都是几十万级别以下的大小，两种方式随意使用，看个人喜欢了。

stackoverflow链接：
http://stackoverflow.com/questions/109383/how-to-sort-a-mapkey-value-on-the-values-in-java