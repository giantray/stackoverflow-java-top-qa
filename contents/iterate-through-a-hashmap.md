# HashMap遍历 #

在Java中有多种遍历HashMAp的方法。让我们回顾一下最常见的方法和它们各自的优缺点。由于所有的Map都实现了Map接口，所以接下来方法适用于所有Map（如：HaspMap，TreeMap,LinkedMap,HashTable,etc）

## 方法#1 使用For-Each迭代entries ##

这是最常见的方法，并在大多数情况下更可取的。当你在循环中需要使用Map的键和值时，就可以使用这个方法

	Map<Integer, Integer> map = new HashMap<Integer, Integer>();
	for(Map.Entry<Integer, Integer> entry : map.entrySet()){
		System.out.println("key = " + entry.getKey() + ", value = " + entry.getValue())
	}

注意：For-Each循环是Java5新引入的，所以只能在Java5以上的版本中使用。如果你遍历的map是null的话，For-Each循环会抛出NullPointerException异常，所以在遍历之前你应该判断是否为空引用。

## 方法#2 使用For-Each迭代keys和values ##

如果你只需要用到map的keys或values时，你可以遍历KeySet或者values代替entrySet

	Map<Integer, Integer> map = new HashMap<Integer, Integer>();

	//iterating over keys only
	for (Integer key : map.keySet()) {
    	System.out.println("Key = " + key);
	}

	//iterating over values only
	for (Integer value : map.values()) {
    	System.out.println("Value = " + value);
	}

这个方法比entrySet迭代具有轻微的性能优势(大约快10%)并且代码更简洁

## 方法#3 使用Iterator迭代 ##

使用泛型

	Map<Integer, Integer> map = new HashMap<Integer, Integer>();
	Iterator<Map.Entry<Integer, Integer>> entries = map.entrySet().iterator();
	while (entries.hasNext()) {
    	Map.Entry<Integer, Integer> entry = entries.next();
    	System.out.println("Key = " + entry.getKey() + ", Value = " + entry.getValue());
	}

不使用泛型

	Map map = new HashMap();
	Iterator entries = map.entrySet().iterator();
	while (entries.hasNext()) {
    	Map.Entry entry = (Map.Entry) entries.next();
    	Integer key = (Integer)entry.getKey();
    	Integer value = (Integer)entry.getValue();
    	System.out.println("Key = " + key + ", Value = " + value);
	}

你可以使用同样的技术迭代keyset或者values

这个似乎有点多余但它具有自己的优势。首先，它是遍历老java版本map的唯一方法。另外一个重要的特性是可以让你在迭代的时候从map中删除entries的(通过调用iterator.remover())唯一方法.如果你试图在For-Each迭代的时候删除entries，你将会得到unpredictable resultes 异常。

从性能方法看，这个方法等价于使用For-Each迭代

## 方法#4 迭代keys并搜索values（低效的） ##

	Map<Integer, Integer> map = new HashMap<Integer, Integer>();
	for (Integer key : map.keySet()) {
    	Integer value = map.get(key);
    	System.out.println("Key = " + key + ", Value = " + value);
	}

这个方法看上去比方法#1更简洁，但是实际上它更慢更低效，通过key得到value值更耗时（这个方法在所有实现map接口的map中比方法#1慢20%-200%）。如果你安装了FindBugs，它将检测并警告你这是一个低效的迭代。这个方法应该避免

## 总结 ##

如果你只需要使用key或者value使用方法#2，如果你坚持使用java的老版本（java 5 以前的版本）或者打算在迭代的时候移除entries，使用方法#3。其他情况请使用#1方法。避免使用#4方法。

stackoverflow链接：
http://stackoverflow.com/questions/1066589/iterate-through-a-hashmap