---
title: Java：泛型与常见数据结构
date: 2020-02-27 16:14:36
categories: 
toc: true
tags: [Java]
---

## Collection

```java
package com.itheima_01;

import java.util.ArrayList;
import java.util.Collection;

/*
 * 	Collection 	
 * 			boolean add(E e)  
 * 			void clear()  
 * 			boolean contains(Object o)  
 * 			boolean isEmpty() 
 * 			boolean remove(Object o) 
 * 			int size() 
 * 			Object[] toArray()  
 *
 * Iterator<E> iterator()  
 * 		
 */
public class CollectionDemo2 {
	public static void main(String[] args) {		
		//创建集合对象
		//Collection c = new Collection();//Collection是接口，不能实例化
		Collection c = new ArrayList();//多态，父类引用指向子类对象  
		
		//boolean add(E e)  
		System.out.println(c.add("hello"));//永远可以添加成功，因为ArrayList他允许重复
		System.out.println(c.add("world"));
		
		//void clear()：清空集合
		//c.clear();
		
		//boolean contains(Object o)  :判断集合中是否包含指定元素
		//System.out.println(c.contains("java"));
		
		//boolean isEmpty() :是否为空
		//System.out.println(c.isEmpty());
				
		//boolean remove(Object o) :删除元素
		//System.out.println(c.remove("java"));
		
		//int size() :返回集合中的元素个数
		//System.out.println(c.size());
		
		//Object[] toArray()  :将集合转换成一个Object类型的数组
		Object[] objs = c.toArray();
		for (int i = 0; i < objs.length; i++) {
			System.out.println(objs[i]);
		}

		System.out.println(c);
	}
}
```

<!-- more -->

* 集合的遍历方式

```java
package com.itheima_02;

import java.util.ArrayList;
import java.util.Collection;
import java.util.Iterator;

/*
 * 集合的遍历方式：
 * 		1.toArray(),可以把集合转换成数组，然后遍历数组即可
 * 		2.iterator(),可以返回一个迭代器对象，我们可以通过迭代器对象来迭代集合
 * 
 * Iterator：可以用于遍历集合
 * 	E next()  :返回下一个元素
 *  boolean hasNext()  ：判断是否有元素可以获取
 * 
 *  注意：Exception in thread "main" java.util.NoSuchElementException
 *  	使用next方法获取下一个元素，如果没有元素可以获取，则出现NoSuchElementException
 */
public class IteratorDemo {
	public static void main(String[] args) {
		//method();
		//创建集合对象
		Collection c = new ArrayList();
		//添加元素
		c.add("hello");
		c.add("world");
		c.add("java");
		
		//获取迭代器对象
		Iterator it = c.iterator();
		
		//Object next()  :返回下一个元素
		//boolean hasNext()  ：判断是否有元素可以获取
		
		/*if(it.hasNext())
			System.out.println(it.next());
		if(it.hasNext())
			System.out.println(it.next());
		if(it.hasNext())
			System.out.println(it.next());
		if(it.hasNext())
			System.out.println(it.next());*/
		
		while(it.hasNext()) {
			System.out.println(it.next());
		}
	}

	private static void method() {
		//创建集合对象
		Collection c = new ArrayList();
		//添加元素
		c.add("hello");
		c.add("world");
		c.add("java");
		//获取数组
		Object[] objs = c.toArray();
		//遍历数组
		for (int i = 0; i < objs.length; i++) {
			System.out.println(objs[i]);
		}
	}
}
```

```java
package com.itheima_02;

import java.util.ArrayList;
import java.util.Collection;
import java.util.Iterator;
import java.util.List;
import java.util.ListIterator;

/*
 * 需求：判断集合中是否包含元素java，如果有则添加元素android
 * Exception in thread "main" java.util.ConcurrentModificationException:并发修改异常
 * 迭代器是依赖于集合的，相当于集合的一个副本，当迭代器在操作的时候，如果发现和集合不一样，则抛出异常
 * 
 * 解决方案:
 * 		你就别使用迭代器
 * 		在使用迭代器进行遍历的时候使用迭代器来进行修改(迭代器会同步集合，List.ListIterator有add方法)
 */
public class IteratorDemo3 {
	public static void main(String[] args) {
		//method();
		
		//创建集合对象
		//Collection c = new ArrayList();
		List c = new ArrayList();
		//添加元素
		c.add("hello");
		c.add("world");
		c.add("java");
		
		//我们可以通过遍历来获取集合中的每一个元素，然后进行比较即可
		/*Iterator it = c.iterator();
		while(it.hasNext()) {
			String s = (String)it.next();
			if(s.equals("java")) {
				c.add("android");
			}
		}*/
		
		ListIterator lit = c.listIterator();
		while(lit.hasNext()) {
			String s = (String)lit.next();
			if(s.equals("java")) {
				lit.add("android");
			}
		}
		
		System.out.println(c);
	}

	private static void method() {
		//创建集合对象
		Collection c = new ArrayList();
		//添加元素
		c.add("hello");
		c.add("world");
		c.add("java");
		//判断集合中是否包含元素java
		if(c.contains("java")) {
			c.add("android");
		}
		
		System.out.println(c);
	}
}
```

## 泛型

```java
package com.itheima_03;

import java.util.ArrayList;
import java.util.Collection;
import java.util.Iterator;

/*
 * 使用集合存储自定义对象并遍历
 * 由于集合可以存储任意类型的对象，当我们存储了不同类型的对象，就有可能在转换的时候出现类型转换异常，
 * 所以java为了解决这个问题，给我们提供了一种机制，叫做泛型
 * 
 * 泛型：是一种广泛的类型，把明确数据类型的工作提前到了编译时期，借鉴了数组的特点
 * 泛型好处：
 * 			避免了类型转换的问题
 * 			可以减少黄色警告线
 * 			可以简化我们代码的书写
 * 
 * 什么时候可以使用泛型？
 * 		问API，当我们看到<E>，就可以使用泛型了
 */
public class GenericDemo {
	public static void main(String[] args) {
		//创建集合对象
		Collection<Student> c = new ArrayList<Student>();
		//创建元素对象
		Student s = new Student("zhangsan",18);
		Student s2 = new Student("lisi",19);
		//添加元素对象
		c.add(s);
		c.add(s2);
		//遍历集合对象
		
		Iterator<Student> it = c.iterator();
		while(it.hasNext()) {
			//String str = (String)it.next();
			//System.out.println(str);
			
			Student stu = it.next();
			System.out.println(stu.name);
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
}
```

## List

```java
package com.itheima_05;

import java.util.ArrayList;
import java.util.List;

/*
 * List:
 * 		有序的（存储和读取的顺序是一致的）
 * 		有整数索引
 * 		允许重复的
 * 
 * List的特有功能：
 * 			void add(int index, E element)  
 * 			E get(int index)  
 * 			E remove(int index)  
 *  		E set(int index, E element)
 *  
 *   增删改查    
 */
public class ListDemo {
	public static void main(String[] args) {
		//创建的列表对象
		List list = new ArrayList();
		
		//void add(int index, E element)  : 在指定索引位置添加指定元素
		list.add(0, "hello");
		list.add(0, "world");
		list.add(1, "java");
		
		//E get(int index)  :根据索引返回元素
		/*System.out.println(list.get(0));
		System.out.println(list.get(1));
		System.out.println(list.get(2));*/
		//System.out.println(list.get(3));
		
		/*for (int i = 0; i < list.size(); i++) {
			System.out.println(list.get(i));
		}*/
		
		//E remove(int index)  : 删除指定元素并返回
		
		//System.out.println(list.remove(5));
		
		//E set(int index, E element) : 将指定索引位置的元素替换为指定元素，并将原先的元素返回
		System.out.println(list.set(0, "android"));
		
		System.out.println(list);
	}
}
```

## 链表

```java
package com.itheima_06;

import java.util.LinkedList;

/*
 * List的常用子类：
 * 		ArrayList
 * 			底层是数组结构，查询快，增删慢
 * 		LinkedList
 * 			底层结构是链表，查询慢，增删快
 * 
 * 如何选择使用不同的集合？
 * 		如果查询多，增删少，则使用ArrayList
 * 		如果查询少，增删多，则使用LinkedList
 * 		如果你不知道使用什么，则使用ArrayList
 * 
 * LinkedList的特有功能：
 * 			void addFirst(E e)  
 * 			void addLast(E e) 
 *			E getFirst()  
 *			E getLast()  
 *			E removeFirst() 
 *			E removeLast()  				
 */
public class LinkedListDemo {
	public static void main(String[] args) {
			LinkedList list = new LinkedList();
			list.add("hello");
			list.add("world");
		
			//void addFirst(E e)  :将元素添加到索引为0的位置
 			//void addLast(E e) ：将元素添加到索引为size()-1的位置
			list.addFirst("java");
			list.addLast("android");
			
 			//E getFirst()  :获取索引为0的元素
 			//E getLast()  ：获取索引为size()-1的元素
			//System.out.println(list.getFirst());
			//System.out.println(list.getLast());
			
 			//E removeFirst() :删除索引为0的元素并返回
 			//E removeLast() ：删除索引为size()-1的元素并返回
			System.out.println(list.removeFirst());
			System.out.println(list.removeLast());
						
			System.out.println(list);
	}
}
```

