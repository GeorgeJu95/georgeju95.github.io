---
title: Java常用API
date: 2020-02-26 13:18:36
categories: 
toc: true
tags: [Java]
---

* 一些Eclipse的小操作
  * 搜索查看Java类源码 Shift + Ctrl + T
  * 抽取方法 Alt + Shift + M

<!-- more -->

## Object

```java
package com.itheima_01;
/*
 * String toString()  : 返回该对象的字符串表示
 * 		 return getClass().getName() + "@" + Integer.toHexString(hashCode());
 * 		 getClass():返回一个字节码对象
 * 		 Integer.toHexString():返回指定参数的十六进制字符串形式
 * 		 hashCode()：返回该对象的哈希码值（内部地址）
 * 
 * boolean equals(Object obj)，见下段代码  
 * 
 */
public class ObjectDemo {
	public static void main(String[] args) {
		Student s = new Student();
		s.name = "zhangsan";
		s.age = 18;
		System.out.println(s.toString());//com.itheima_01.Student@737951b0
		System.out.println(s);//说明我们输出一个对象就是默认输出这个对象的toString()方法
	}
}

class Student extends Object {
	String name;
	int age;
	/* 重写toString()
	public String toString() {
		return name + "@" + age;
	}
	*/
	
  // source - generate toString() Eclipse提供的功能
	@Override
	public String toString() {
		return "Student [name=" + name + ", age=" + age + "]";
	}	
}
```

```java
package com.itheima_01;

import java.util.ArrayList;

/*
 * boolean equals(Object obj)  
 * 		使用==来比较两个对象是否相等，则比较地址值是否相等
 */
public class ObjectDemo2 {
	public static void main(String[] args) {
		Person p = new Person("zhangsan",18);
		Person p2 = new Person("zhangsan",19);
		
		//boolean flag = p.equals(p2);
		//boolean flag = p.equals(p);
		boolean flag = p.equals(new ArrayList());
		System.out.println(flag);
	}
}

class Person {
	String name;
	int age;
	
	public Person(String name,int age) {
		this.name = name;
		this.age = age;
	}

  // source - generate hashCode & equals，删掉了hashCode
	@Override
	public boolean equals(Object obj) {
		//提高效率
		if (this == obj)
			return true;
		
		if (obj == null)
			return false;
		//提高健壮性
		if (getClass() != obj.getClass())
			return false;
		
		//向下转型
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
	
	/*@Override
	public boolean equals(Object o) {
		//提高效率 当前对象和传递进来的对象地址值一样，则不用比较成员
		if(this == o) {
			return true;
		}
		
		//提高代码的健壮性
		if(this.getClass() != o.getClass()) {
			return false;
		}
		
		//向下转型
		Person other = (Person) o;
		
		if(!this.name.equals(other.name)) {
			return false;
		}
		
		if(this.age != other.age) {
			return false;
		}
		
		return true;
	}*/	
}
```

## System

System 类包含一些有用的类字段( static所修饰的成员变量 )和类方法。它不能被实例化。

```java
package com.itheima_02;
/*
 * System:包含一些有用的类字段和方法。它不能被实例化
 * static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length)  
 * static long currentTimeMillis()  
 * static void exit(int status) 
   static void gc()  // 尽最大努力去回收，因此有可能调用了，重写的finalize()也没有执行
 * 
 */
public class SystemDemo {
	public static void main(String[] args) {
		//method();
		//method2();
		//method3();
		
		//static void gc()  
		//Demo d = new Demo();
		new Demo();
		System.gc();
	}

	private static void method3() {
		//static void exit(int status) :终止虚拟机
		
		for (int i = 0; i < 100000; i++) {
			System.out.println(i);
			if(i == 100) {
				System.exit(0);
			}
		}
	}

	private static void method2() {
		/*
		 *  static long currentTimeMillis() :以毫秒值返回当前系统时间
		 *  这个毫秒的时间是相对时间，相对于1970-1-1 00:00:00 ： 0
		 *  1970-1-1 00:00:01 : 1000
		 *  1970-1-1 00:01:00: 1000 * 60
		 *  1970-1-1 01:00:00: 1000 * 60 * 60
		 *  1000毫秒 = 1秒
		 *  
		 */
		//System.out.println(System.currentTimeMillis());
		
		
		long start = System.currentTimeMillis();
		for (int i = 0; i < 100000; i++) {
			System.out.println(i);
		}
		long end = System.currentTimeMillis();
		System.out.println(end - start);
	}

	private static void method() {
		/*
		 * static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length)  
		 * 复制数组
		 * 参数1：源数组
		 * 参数2：源数组的起始索引位置
		 * 参数3：目标数组
		 * 参数4：目标数组的起始索引位置
		 * 参数5：指定接受的元素个数
		 */
		int[] src = {1,2,3,4,5};
		int[] dest = new int[5];
		System.arraycopy(src, 2, dest, 4, 3);
		
		for (int i = 0; i < dest.length; i++) {
			System.out.print(dest[i]);
		}
	}
}

class Demo {
	@Override
	protected void finalize() throws Throwable {
		System.out.println("我被回收了");
	}
}
```

## Date

```java
package com.itheima_03;

import java.util.Date;

/*
 * Date: 表示特定的瞬间，精确到毫秒，他可以通过方法来设定自己所表示的时间，可以表示任意的时间
 * System.currentTimeMillis():返回的是当前系统时间，1970-1-1至今的毫秒数
 *  
 * 构造方法：
 * 		Date() ：创建的是一个表示当前系统时间的Date对象
		Date(long date) ：根据"指定时间"创建Date对象
 
 */
public class DateDemo {
	public static void main(String[] args) {
		//Date()
		//Date d = new Date();
		//System.out.println(d);//Thu Aug 26 14:17:28 CST 2049
		//System.out.println(d.toLocaleString()); //已过时
		
		//Date(long date) 
		Date d2 = new Date(1000 * 60 * 60 * 24);//时区 有时差
		System.out.println(d2.toLocaleString());
	}
}
```

```java
package com.itheima_03;

import java.util.Date;

/*
 * Date的常用用方法
		毫秒值 --- Date
			设置
			返回值是void，参数long
			void setTime(long time)  
			Date(long date)
		Date --- 毫秒值
			获取
			返回long，无参数
			long getTime()  
 */
public class DateDemo2 {
	public static void main(String[] args) {
		Date d = new Date();//默认当前系统时间
		//d.setTime(1000 * 60 * 60 * 24 * 2);
		System.out.println(d.toLocaleString());
		System.out.println(d.getTime());//172800000
				
		d.setTime(172800000L);
		System.out.println(d.toLocaleString());
	}
}
```

## DateFormat

* DateFormat类是对日期进行格式化的类 

* DateFormat类的使用
  * 本身是一个抽象类，所以必须使用的时候使用的是其子类SimpleDateFormat，并且在子类中有很多模式字母需要记性。
  * DateFormat要掌握的功能
    * 从日期到字符串的转换（格式化）
    * 从字符串到日期的转换（解析）

```java
package com.itheima_04;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

/*
 * SimpleDateFormat:
 * 		格式化：
 * 			Date --- String
 * 			2049-8-26 2049年8月26日
 * 			String format(Date date) 
 * 		解析：
 * 			String --- Date
 * 			"2049-8-26"
 * 			Date parse(String source) 
 * 
 * 构造方法：
 * 		SimpleDateFormat() ：使用默认的模式进行对象的构建
 * 		SimpleDateFormat(String pattern) ：使用的指定的模式进行对象的构建
 * 
 * 注意：Exception in thread "main" java.text.ParseException: Unparseable date: "49年9月26日  下午1:29"
 * 	    解析的字符串，模式必须和构建对象的模式一样
 */
public class SimpleDateFormatDemo {
	public static void main(String[] args) throws ParseException {
		//method();
		//method2();
		//使用指定的模式进行对象的构建
		//1999年9月1日 10:10:10
		//4个小姨2个大美眉和2个小弟弟
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
		
		//格式化
		Date date = new Date();
		String s = sdf.format(date);
		System.out.println(s);//2049年08月26日 13:39:12

		//解析
		Date d = sdf.parse("2049年08月26日 13:39:12");
		System.out.println(d.toLocaleString());
	}

	private static void method2() throws ParseException {
		//使用指定的模式进行对象的构建
		//1999年9月1日
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日");
		//格式化
		Date date = new Date();
		String s = sdf.format(date);
		System.out.println(s);//2049年08月26日
		
		//解析
		Date d = sdf.parse("2049年08月26日");
		System.out.println(d.toLocaleString());
	}

	private static void method() throws ParseException {
		//使用默认模式进行对象的构建
		SimpleDateFormat sdf = new SimpleDateFormat();
		//创建日期对象
		Date date = new Date();
		
		//格式化 把日期对象转换成字符串
		String s = sdf.format(date);
		System.out.println(s);//49-8-26 下午1:29
		
		//解析 把字符串转换成日期对象
		Date d = sdf.parse("49年9月26日  下午1:29");
		System.out.println(d.toLocaleString());
	}
}
```

## Calendar

```java
package com.itheima_05;

import java.util.Calendar;

/*
 * Calendar：日历，提供了一些操作年月日时的方法
 * 
 * 获取
 * 修改
 * 添加
 */
public class CalendarDemo {
	public static void main(String[] args) {
		//static Calendar getInstance()  
		Calendar c = Calendar.getInstance();
		
		//void set(int field, int value) ：把指定的字段修改成指定的值
		//c.set(Calendar.DAY_OF_MONTH, 20);
		
		//void add(int field, int amount): 在指定的字段上加上指定的值
		c.add(Calendar.DAY_OF_MONTH, -1);
		
		//int get(int field) // 返回给定日历字段的值
		//public static final int YEAR 1 
		//System.out.println(Calendar.YEAR);
		
		//int year = c.get(1);
		int year = c.get(Calendar.YEAR);
		int month = c.get(Calendar.MONTH) + 1;
		int day = c.get(Calendar.DAY_OF_MONTH);
			
		System.out.println(year + "年" + month + "月" + day + "日");	 
	}
}
```

## 包装类

 ```java
package com.itheima_06;
/*
 * 需求：判断一个数是否符合int类型的范围
 * 由于基本数据类型只能做一些简单的操作和运算，所以Java为我们封装了基本数据类型，为每种基本数据类型提供了包装类
 * 包装类就是封装了基本数据类型的类，为我们提供了更多复杂的方法和一些变量
 * 
 * byte		Byte
 * short	Short
 * char		Character
 * int		Integer
 * long		Long
 * float	Float
 * double	Double
 * boolean	Boolean
 * 
 * Integer:
 * 		String --- int
 * 			方式1：int intValue()
 * 			方式2： static int parseInt(String s) 
 * 		int --- String
 * 			方式1： + ""
 * 			方式2：String toString()
 * 
 * 构造方法：
 * 		Integer(int value) 
 * 		Integer(String s) 
 */
public class IntegerDemo {
	public static void main(String[] args) {
		/*int n = 10;
		if(n >= Math.pow(-2, 31) && n <= Math.pow(2, 31) -1) {
			System.out.println("符合");
		}
		else {
			System.out.println("不符合");
		}*/
		
		//Integer(String s) 
		//Integer i = new Integer("10");
		//System.out.println(i);
		
		//int intValue()  
		//int a = i.intValue();
		//System.out.println(a + 10 );
		
		//static int parseInt(String s) 
		//int b = Integer.parseInt("20");
		//System.out.println(b + 30);
		
		
		//Integer(int value)
		//Integer i2 = new Integer(40);
		//String s = i2.toString();
		//System.out.println(s);
		
		//static String toString(int i)  
		//String s2 = Integer.toString(50);
		//System.out.println(s2);
		
	}
}
 ```

#### 自动装箱和拆箱

 ```java
package com.itheima_06;

import java.util.ArrayList;

/*
 * JDK1.5特性：自动装箱和拆箱
 */
public class IntegerDemo2 {
	public static void main(String[] args) {
		//Integer i = new Integer(10);
		
		//自动装箱
		//相当于： Integer i = new Integer(10);
		//Integer i = 10;
		
		//自动拆箱
		//相当于 int a = i.intValue();
		//Integer i = 10;
		//int a = i;
		
		Integer i = 10;
		Integer i2 = 20;
		Integer i3 = i + i2;
		/*
		 * Integer i3 = new Integer(i.intValue() + i2.intValue());
		 */
		
		ArrayList list = new ArrayList();
		list.add(1);//自动装箱，list.add(new Integer(1));
	}
}
 ```

## 正则表达式

```java
package com.itheima_07;
/*
 * 	校验qq号码
 * 		要求必须是5-15位
 * 		0不能开头
 * 		必须都是数字
 *
 *  正则表达式：就是一套规则，可以用于匹配字符串
 *  boolean matches(String regex) ：判断当前字符串是否匹配指定的正则表达式，如果匹配则返回true，否则返回false  
 */
public class RegexDemo {
	public static void main(String[] args) {
		String qq = "12a345";
		/*boolean flag = checkQQ(qq);
		System.out.println(flag);*/
			
		boolean flag = qq.matches("[1-9][0-9]{4,14}");
		System.out.println(flag);
	}
	
	public static boolean checkQQ(String qq) {
		int length = qq.length();
		//要求必须是5-15位
		if(length < 5 || length > 15) {
			return false;
		}
		
		//0不能开头
		if(qq.startsWith("0")) {
			return false;
		}
		
		//必须都是数字
		for (int i = 0; i < length; i++) {
			//得到参数的每一个字符
			char c = qq.charAt(i);
			if(c < '0' || c > '9') {
				return false;
			}
		}
	
		return true;//符合要求
	}	
}
```

