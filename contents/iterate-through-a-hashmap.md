##如何遍历map对象（如HashMap)

###jdk1.5以上版本
```java
for (Entry<String, String> entry : map.entrySet()){
  System.out.println(entry.getKey() + "/" + entry.getValue());
}
```
需要 import java.util.Map.Entry;

###jdk1.4以下版本
```java
Iterator entries = myMap.entrySet().iterator();
while (entries.hasNext()) {
  Entry thisEntry = (Entry) entries.next();
  Object key = thisEntry.getKey();
  Object value = thisEntry.getValue();
}
```

stackoverflow链接：
http://stackoverflow.com/questions/1066589/iterate-through-a-hashmap