---
title: Java：Map & Set
date: 2020-02-28 20:17:36
categories: 
toc: true
tags: [Java]
---

## HashSet

```java
package com.itheima_01;

import java.util.HashSet;

/*
 *	使用HashSet存储字符串并遍历 	
 *	
 *	Set的特点：
 *			 无序（存储和读取的顺序可能不一样）
 *			不允许重复
 *			没有整数索引
 */
public class HashSetDemo {
	public static void main(String[] args) {
		//创建集合对象
		//Set set = new HashSet();
		HashSet<String> set = new HashSet<String>();
		//添加元素
		set.add("hello");
		set.add("world");
		System.out.println(set.add("java"));
		System.out.println(set.add("java"));
		
		//遍历集合对象
		for(String s : set) {
			System.out.println(s);
		}	
	}
}

```

<!-- more -->

* HashSet方法的add()方法解析

```java
 //K key：要添加的新元素
 public V put(K key, V value) {
        //根据新添加元素的hashCode()返回值计算出hash值
        int hash = hash(key);
        int i = indexFor(hash, table.length);
        
        //获取当前集合中的每一个元素
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            //和新添加的元素进行比较，判断是否重复，如果重复则返回，
            //先比较hash值，hash值不一样if语句结束
            //如果hash值一样，则比较地址在或者使用equals方法进行比较
            //比较结果如果为true，则重复不在添加
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }

        modCount++;
        //直接添加元素
        addEntry(hash, key, value, i);
        return null;
    }
```

```java
package com.itheima_01;

import java.util.HashSet;

/*
 *	使用HashSet存储自定义对象并遍历 	
 *	通过查看源码发现：
 *				HashSet的add()方法，首先会使用当前集合中的每一个元素和新添加的元素进行hash值比较，
 *				如果hash值不一样，则直接添加新的元素
 *				如果hash值一样，比较地址值或者使用equals方法进行比较
 *				比较结果一样，则认为是重复不添加
 *				所有的比较结果都不一样则添加
 */
public class HashSetDemo2 {
	public static void main(String[] args) {
		//创建集合对象
		HashSet<Student> hs = new HashSet<Student>();
		//创建元素对象
		Student s = new Student("zhangsan",18);
		Student s2 = new Student("lisi",19);
		Student s3 = new Student("lisi",19);
		//添加元素对象
		hs.add(s);
		hs.add(s2);
		hs.add(s3);
		//遍历集合对象
		for (Student student : hs) {
			System.out.println(student);
		}		 
	}		 
}

class Student {
	String name;
	int age;
	
	public Student(String name,int age) {
		this.name = name;
		this.age = age;
	}

	@Override
	public String toString() {
		return "Student [name=" + name + ", age=" + age + "]";
	}
	
	@Override
	public boolean equals(Object obj) {
		//System.out.println("-------------------");
		Student s = (Student)obj;//向下转型，可以获取子类特有成员
		
		//比较年龄是否相等，如果不等则返回false
		if(this.age != s.age) {
			return false;
		}
		
		//比较姓名是否相等，如果不等则返回false
		if(!this.name.equals(s.name)) {
			return false;
		}
		
		//默认返回true，说明两个学生是相等的
		return true;
	}
	
	@Override
	public int hashCode() {
    //返回一样的值，从而执行我们重写后的equals()方法
    //这样子比较次数还是多了，可以让hashCode()方法和成员变量挂钩，下面的代码是优化
		return 1;
	}	
}
```

```java
[Person.java]
package com.itheima_02;

public class Person {
	String name;
	int age;
	
	public Person(String name,int age) {
		this.name = name;
		this.age = age;
	}

	@Override
	public String toString() {
		return "Person [name=" + name + ", age=" + age + "]";
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + age;
		result = prime * result + ((name == null) ? 0 : name.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Person other = (Person) obj;
		if (age != other.age)
			return false;
		if (name == null) {
			if (other.name != null)
				return false;
		} else if (!name.equals(other.name))
			return false;
		return true;
	}
	
	/*
	@Override
	public int hashCode() {
		
		 * 我们发现当hashCode方法永远返回整数1时，所有对象的hash值都是一样的，
		 * 有一些对象他的成员变量完全不同，但是他们还需要进行hash和equals方法的比较，
		 * 如果我们可以让成员变量不同的对象，他们的hash值也不同，这就可以减少一部分equals方法的比较
		 * 从而可以提高我们程序的效率
		 * 
		 * 可以尝试着让hashCode方法的返回值和对象的成员变量有关
		 * 可以让hashCode方法返回所有成员变量之和，
		 * 让基本数据类型直接想加，然后引用数据类型获取hashCode方法返回值后再相加（boolean不可以参与运算）
		 * 
		 
		//return age;
		return age + name.hashCode();
	}
	
	@Override
	public boolean equals(Object obj) {
		System.out.println("-------------");
		
		//提高效率
		if(this == obj) {
			return true;
		}
		
		//提高健壮性
		if(this.getClass() != obj.getClass()) {
			return false;
		}
			
		//向下转型
		Person p = (Person)obj;
		
		if(!this.name.equals(p.name)) {
			return false;
		}
		
		if(this.age != p.age) {
			return false;
		}
		
		return true;
	}*/
}


[HashSetDemo3.java]
package com.itheima_02;

import java.util.HashSet;

public class HashSetDemo3 {
	public static void main(String[] args) {
		//创建集合对象
		HashSet<Person> hs = new HashSet<Person>();
		//创建元素对象
		Person p = new Person("zhangsan",20);
		Person p2 = new Person("lisi",20);
		Person p3 = new Person("lisi",20);
		//添加元素对象
		hs.add(p);
		hs.add(p2);
		hs.add(p3);
		//遍历集合对象
		for (Person person : hs) {
			System.out.println(person);
		}		
	}
}
```

## Collections

 ```java
package com.itheima_03;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

/*
 * Collections：
 * 面试题：Collection和Collections有什么区别？
 * 		Collection是集合体系的最顶层，包含了集合体系的共性
 * 		Collections是一个工具类，方法都是用于操作Collection
 * 
 */
public class CollectionsDemo {
	public static void main(String[] args) {
		//static void swap(List list, int i, int j) :将指定列表中的两个索引进行位置互换
		List<Integer> list = new ArrayList<Integer>();
		list.add(1);
		list.add(4);
		Collections.swap(list, 0, 1);
		
		System.out.println(list);
	
	}

	private static void method6() {
		//static void  sort(List<T> list) :按照列表中元素的自然顺序进行排序
		List<Integer> list = new ArrayList<Integer>();
		list.add(1);
		list.add(4);
		list.add(3);
		list.add(2);
		
		Collections.sort(list);
		System.out.println(list);
	}

	private static void method5() {
		//static void shuffle(List list):傻否，随机置换  
		List<Integer> list = new ArrayList<Integer>();
		list.add(1);
		list.add(2);
		list.add(3);
		list.add(4);
		Collections.shuffle(list);
		System.out.println(list);
	}

	private static void method4() {
		//static void reverse(List list)  :反转
		List<Integer> list = new ArrayList<Integer>();
		list.add(1);
		list.add(2);
		list.add(3);
		list.add(4);
		
		Collections.reverse(list);
		System.out.println(list);
	}

	private static void method3() {
		//static void fill(List list, Object obj) :使用指定的对象填充指定列表的所有元素
		List<String> list = new ArrayList<String>();
		list.add("hello");
		list.add("world");
		list.add("java");
		System.out.println(list);
		
		Collections.fill(list, "android");
		
		System.out.println(list);
	}

	private static void method2() {
		//static void copy(List dest, List src) :是把源列表中的数据覆盖到目标列表
		//注意：目标列表的长度至少等于源列表的长度
		//创建源列表
		List<String> src = new ArrayList<String>();
		src.add("hello");
		src.add("world");
		src.add("java");
		
		//创建目标列表
		List<String> dest = new ArrayList<String>();
		dest.add("java");
		dest.add("java");
		dest.add("java");
		dest.add("java");
		Collections.copy(dest, src);
		System.out.println(dest);
	}

	private static void method() {
		//static int  binarySearch(List list, Object key) 使用二分查找法查找指定元素在指定列表的索引位置 
		List<Integer> list = new ArrayList<Integer>();
		list.add(1);
		list.add(2);
		list.add(3);
		list.add(4);
		
		int index = Collections.binarySearch(list, 4);
		System.out.println(index);
	}
}
 ```

## Map

需求：实现学号和姓名这样有对应关系的数据存储
 * 为了体现这种有对应关系的数据，我们使用以前所学的内容是可以实现的，但是略有不变，所以java又给我买提供了一种专门用于存储对应关系的集合，Map
 * Map:将键映射到值的对象。一个映射不能包含重复的键；每个键最多只能映射到一个值
 * Map和Collection有什么区别？
    * 			Map:是一个双列集合，常用语处理有对应关系的数据，key是不可以重复的，我们也称之为是夫妻对集合
    * 			Collection:是单列集合，Collection有不同的子体系，有的允许重复有索引有序，有的不允许重复而且无序，那么我们也称之为单身汉集合

```java
package com.itheima_01;

import java.util.HashMap;
import java.util.Map;

/*
 * 	Map的常用功能：
 * 		映射功能：
 * 			 	V put(K key, V value) 
 * 		获取功能：
 * 				V get(Object key) 
 * 				int size() 
 * 		判断功能：
 * 				boolean containsKey(Object key) 
 *				boolean containsValue(Object value) 
 *				boolean isEmpty() 
 * 		删除功能：
 * 				void clear()  
 * 				V remove(Object key)   
 * 		遍历功能：
 * 				Set<Map.Entry<K,V>> entrySet() 
 *
 * 			Set<K> keySet()  
 * 			Collection<V> values()  
 */
public class MapDemo2 {
	public static void main(String[] args) {
		//创建Map对象
		Map<String,String> map = new HashMap<String,String>();
		
		//V put(K key, V value) ：就是将key映射到value，如果key存在，则覆盖value，并将原来的value返回
		System.out.println(map.put("ITCAST001", "张三"));
		System.out.println(map.put("ITCAST002", "李四"));
		System.out.println(map.put("ITCAST001", "王五"));
		
		//void clear() : 清空所有的对应关系  
		//map.clear();
		
		//V remove(Object key) :根据指定的key删除对应关系，并返回key所对应的值，如果没有删除成功则返回null
		//System.out.println(map.remove("ITCAST005"));
		
		//boolean containsKey(Object key) : 判断指定key是否存在
		//System.out.println(map.containsKey("ITCAST003"));
		
		//boolean containsValue(Object value)：判断指定的value是否存在
		//System.out.println(map.containsValue("王五"));
		
		//boolean isEmpty() : 判断是否有对应关系
		//System.out.println(map.isEmpty());
		
		//int size() : 返回对应关系的个数
		//System.out.println(map.size());
		
		//V get(Object key) : 根据指定的key返回对应的value
		System.out.println(map.get("ITCAST002"));
		
		System.out.println(map);
	}
}
```

```java
package com.itheima_01;

import java.util.Collection;
import java.util.HashMap;
import java.util.Map;
import java.util.Set;

/*
 * 	Set<K> keySet()  
 * 	Collection<V> values() 
 */
public class MapDemo3 {
	public static void main(String[] args) {
		//创建Map对象
		Map<String,String> map = new HashMap<String,String>();
		//添加映射关系
		map.put("ITCAST001", "张三");
		map.put("ITCAST002", "李四");
		map.put("ITCAST005", "李四");
		
		//Set<K> keySet() : 以Set的形式获返回所有的key，因为不允许重复
		Set<String> keys = map.keySet();
		for (String key : keys) {
			System.out.println(key);
		}
		System.out.println("-----------");
		
		//Collection<V> values() :
		Collection<String> values = map.values();
		for (String value : values) {
			System.out.println(value);
		}
				
	}
}
```

```java
Map的第一种遍历方式

package com.itheima_01;

import java.util.HashMap;
import java.util.Map;
import java.util.Set;

/*
 * 	Map的第一种遍历方式：
 * 			首先召集所有的丈夫
 * 			遍历所有的丈夫
 * 			获取每一个丈夫
 * 			让每一个丈夫去找他自己的媳妇
 */
public class MapDemo4 {
	public static void main(String[] args) {
		//创建Map对象
		Map<String,String> map = new HashMap<String,String>();
		//添加映射关系
		map.put("谢婷疯", "张箔纸");
		map.put("陈关西", "钟欣桶");
		map.put("李亚碰", "王飞");
		//遍历Map对象
		
		//首先召集所有的丈夫
		Set<String> keys = map.keySet();
		//遍历所有的丈夫
		for (String key : keys) {
			//让每个丈夫去找他自己的媳妇就可以了
			String value = map.get(key);
			System.out.println("丈夫：" + key + "---" + "媳妇：" + value);
		}
		
	}
}


Map的第二种遍历方式

package com.itheima_01;

import java.util.HashMap;
import java.util.Map;
import java.util.Set;

/*
 * 	Map的第二种遍历方式：
 * 		通过结婚证对象来获取丈夫和媳妇
 * 
 *  class 结婚证<K,V> {
 *  	K 丈夫;
 *  	V 媳妇;
 *  
 *  	public 结婚证(K 丈夫，V 媳妇) {
 *  		this.丈夫 = 丈夫;
 *  		this.媳妇 = 媳妇;
 *  	}
 *  
 *  
 *  	public K get丈夫() {
 *  		return 丈夫;
 *  	}
 *  
 *  	public V get媳妇() {
 *  		return 媳妇;
 *  	}
 *  }
 *  
 *  
 *  class Entry<K,V> {
 *  	K key;
 *  	V value;
 *  
 *  	public Entry(K key，V value) {
 *  		this.key = key;
 *  		this.value = value;
 *  	}
 *  
 *  
 *  	public K getKey() {
 *  		return key;
 *  	}
 *  
 *  	public V getValue() {
 *  		return value;
 *  	}
 *  }
 *  
 *  Set<Map.Entry<K,V>> entrySet()  
 * 
 */
public class MapDemo5 {
	public static void main(String[] args) {
		//创建Map对象
		Map<String,String> map = new HashMap<String,String>();
		//添加映射关系
		map.put("尹志平", "小龙女");
		map.put("令狐冲", "东方菇凉");
		map.put("玄慈", "叶二娘");
		//获取所有的结婚证对象
		Set<Map.Entry<String,String>> entrys = map.entrySet();
		//遍历包含了结婚证对象的集合
		for (Map.Entry<String, String> entry : entrys) {
			//获取每个单独的结婚证对象
			//通过结婚证对象获取丈夫和媳妇
			String key = entry.getKey();
			String value = entry.getValue();
			
			System.out.println("丈夫：" + key + "---" + "媳妇:" + value);
		}
	}
}
```

