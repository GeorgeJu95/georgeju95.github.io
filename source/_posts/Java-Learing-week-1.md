---
title: Java的一些基础：数组、方法、集合、IO等
date: 2019-011-08 15:36:36
categories: 
toc: true
tags: [Java]
---

## day 1

* Eclipse快捷键

  * main -- Alt + / -- 回车
  * syso -- Alt + / -- 回车
  * 单行注释 ctrl + /，取消  ctrl + /
  * 多行注释 ctrl + shift + /  取消 ctrl + shift + \
  * 格式化，ctrl shift f  或者 右键--Source--Format
  * 导包，方式1：自己打，方式2：点击左边错误标记，**方式3**：ctrl + shift + o

<!-- more -->

* 字符串 ASCII码 

  * 'A'  65
  * 'a'  97
  * '0'  48

* 字符串参与 + 运算，做的是**字符串的拼接**

  * a = 10; b = 20;
  * "hello" + a + b 即 hello1020
  * a + b "hello"    即 30hello

* 扩展的赋值运算符（+=、-= ......），隐含了**强制类型转换**

  ```java
  short s = 1;
  s += 1;                 // 如果改为普通的赋值运算 s = s + 1，则会报错
  System.out.println(s);  // 输出2
  ```

* 单 & 和 && 的区别之一，&& 有 **短路效果**

* 键盘录入数据

  ```java
  package com.itheima;
  /*
   * Scanner获取键盘输入录入数据的步骤：
   * 1、导包
   * 		import java.util.Scanner;
   * 		在一个类中顺序：package > import > class
   * 2、创建键盘录入对象
   * 		Scanner sc = new Scanner(System.in);
   * 3、接收数据
   * 		int i = sc.nextInt();
   */
  import java.util.Scanner;
  
  public class ScannerDemo {
  	public static void main(String[] args) {
  		Scanner sc = new Scanner(System.in);
  		System.out.println("Please input a int Num");
  		int i = sc.nextInt();
  		System.out.println("i = " + i);
  	}
  }
  ```

## day 2

* **Random**用于产生随机数

  ```java
  package com.itheima;
  
  import java.util.Random;
  
  /*
   * Random:用于产生随机数
   * 
   * 使用步骤：
   * 		A:导包
   * 			import java.util.Random;
   * 		B:创建对象
   * 			Random r = new Random();
   * 		C:获取随机数
   * 			int number = r.nextInt(10);
   * 			获取数据的范围：[0,10) 包括0,不包括10
   */
  public class RandomDemo {
  	public static void main(String[] args) {
  		//创建对象
  		Random r = new Random();
  		
  		for(int x=1; x<=10; x++) {
  			//获取随机数
  			int number = r.nextInt(10);
  			System.out.println("number:"+number);
  		}
  		System.out.println("------------------");
  		
  		//如何获取1-100之间的随机数呢?
  		//int i = r.nextInt(100); //[0,99]
  		int i = r.nextInt(100) + 1;
  		System.out.println("i:"+i);
  	}
  }
  ```

* 数组初始化

  * 动态初始化	数据类型[] 数组名 = new 数据类型[数组长度];
  * 静态初始化	数据类型[] 数组名 = new 数据类型[]{元素1, 元素2, 元素3,...};

* Java中的内存分配：

  * **栈**（存储的是局部变量）
    * 使用完毕，立即回收
  * **堆**（存储的是new出来的东西，**实体**，**对象**）
    * 每一个对象都有**地址值**
    * 每一个对象的数据都有**默认值**
    * 使用完毕后，会在垃圾回收器空闲的时候回收
    * byte，short，int，long  0
    * float，double  0.0
    * char  '\u0000'
    * boolean  false
    * 引用类型  null
  * **方法区**（面向对象）
  * **本地方法区**（和系统相关）
  * **寄存器**（给CPU使用）

* 数组操作中两个常见小问题

  ```java
  package com.itheima_03;
  /*
   * 两个常见小问题：
   * 		ArrayIndexOutOfBoundsException:数组索引越界异常
   * 			产生的原因：我们访问了不存在的索引
   * 
   * 		NullPointerException:空指针异常
   * 			产生的原因：数组已经不在指向堆内存的数据了，你还使用数组名去访问元素
   */
  public class ArrayDemo {
  		public static void main(String[] args) {
  			//定义数组
  			int[] arr = {1,2,3};
  			
  			//访问数组元素，越界
  			//System.out.println(arr[3]);
  			
  			//引用类型：类,接口,数组
  			//常量：空常量,null,是可以赋值给引用类型的
  			arr = null;
  			//空指针异常
  			System.out.println(arr[1]);
  		}
  }
  ```

* 二维数组

  ```java
  package com.itheima_05;
  /*
   * 二维数组：其实就是元素为一维数组的数组。
   * 
   * 定义格式：
   * 		A:数据类型[][] 数组名; (推荐的方式)
   * 		B:数据类型 数组名[][];
   * 		C:数据类型[] 数组名[];
   * 初始化：
   * 		A:动态初始化
   * 			数据类型[][] 数组名 = new 数据类型[m][n];
   * 			m表示的是二维数组中一维数组的个数
   * 			n表示的是一维数组中的元素个数
   * 		B:静态初始化
   * 			数据类型[][] 数组名 = new 数据类型[][]{{元素...},{元素...},{元素...},...};
   * 			简化格式：
   * 			数据类型[][] 数组名 = {{元素...},{元素...},{元素...},...};
   * 
   * 二维数组名配合索引可以获取到每一个一维数组。
   * 每一个一维数组配合索引名可以获取到数组中的元素。
   * 
   * 假如我有一个二维数组：arr。
   * 我要从中获取一维数组：arr[索引]
   * 我要从中获取二维数组的元素：arr[索引][索引]
   */
  public class ArrayArrayDemo {
  	public static void main(String[] args) {
  		//数据类型[][] 数组名 = {{元素...},{元素...},{元素...},...};
  		int[][] arr = {{1,2,3},{4,5,6},{7,8,9}};
  		
  		System.out.println(arr); //[[I@1774b9b
  		System.out.println(arr[0]); //[I@104c575
  		System.out.println(arr[1]);
  		System.out.println(arr[2]);
  		
  		//如何获取二维数组中的元素呢?
  		System.out.println(arr[0][0]);
  		System.out.println(arr[1][1]);
  		System.out.println(arr[2][2]);
  	}
  }
  ```


## day 3

* 方法重载

  ```java
  package com.itheima_03;
  /*
   * 方法重载：在同一个类中，出现了方法名相同的情况。
   * 方法重载的特点：
   * 		方法名相同，参数列表不同。与返回值无关。
   * 		参数列表不同：
   * 			参数的个数不同
   * 			参数对应的数据类型不同
   * 
   * 注意：
   * 		在调用方法的时候，java虚拟机会通过参数列表的不同来区分同名的方法。
   */
  public class MethodDemo {
  	public static void main(String[] args) {
  		//定义变量
  		int a = 10;
  		int b = 20;
  		
  		//求和方法
  		int result = sum(a,b);
  		System.out.println("result:"+result);
  		
  		//定义变量
  		int c = 30;
  		//求和方法
  		//int result2 = sum2(a,b,c);
  		int result2 = sum(a,b,c);
  		System.out.println("result2:"+result2);
  		
  	}
  	
  	//不能出现方法名相同，并且参数列表也相同的情况
  //	public static int sum(int x,int y) {
  //		return x + y;
  //	}
  	
  	public static float sum(float a,float b) {
  		return a + b;
  	}
  	
  	//求三个数据的和
  	/*
  	public static int sum2(int a,int b,int c) {
  		return a + b + c;
  	}
  	*/
  	public static int sum(int a,int b,int c) {
  		return a + b + c;
  	}
  	
  	//求两个数据的和方法
  	public static int sum(int a,int b) {
  		//int c = a + b;
  		//return c;
  		
  		return a + b;
  	}
  }
  ```

* 形式参数与实际参数

  * 如果方法参数类型是基本数据类型，形式参数的改变不影响实际参数
  * 如果方法参数类型是引用数据类型，形式参数的改变直接影响实际参数

* **Eclipse 断点调试**

  ```
  1、加断点，双击左边。
  2、Step Over F6
  看哪些区域
  	代码区域：看程序的执行流程
  	Debug区域：看程序的执行流程
  	Variables：看变量的变化
  	Console：看程序的输入和输出
  3、取消断点
  	方法1：双击左边断点处
  	方法2：Debug视图 -- 断点 -- 除去所有断点（或选择删除）
  ```


## day 4 String 和 StringBuilder

![compare](/assets/blogImg/compare.png)

* 通过构造方法创建字符串对象是在堆内存，直接赋值方式创建对象是在方法区的常量池。

* String类可以通过 length()和charAt() 方法遍历，也可以先toCharArray转换成字符数组，再利用属性 length 和数组下标遍历。

![caocatenate](/assets/blogImg/concatenate.png)

* 如果对字符串进行拼操作，每次拼接，都会构建一个新的 String 对象，既耗时，又浪费空间。而StringBuilder就可以解决这个问题，因为它是**可变的字符序列。也被称为字符串缓冲区类**。

  ```java
  package com.itheima_02;
  /*
   * 添加功能
   *		public StringBuilder append(任意类型):添加数据，并返回自身对象
   * 反转功能
   *		public StringBuilder reverse()
   */
  public class StringBuilderDemo {
  	public static void main(String[] args) {
  		//创建对象
  		StringBuilder sb = new StringBuilder();
  		
  		//public StringBuilder append(任意类型)
  		//StringBuilder sb2 = sb.append("hello");
  		
  		/*
  		System.out.println("sb:"+sb);
  		System.out.println("sb2:"+sb2);
  		System.out.println(sb == sb2); //true
  		*/
  		
  		/*
  		sb.append("hello");
  		sb.append("world");
  		sb.append(true);
  		sb.append(100);
  		*/
  		
  		//链式编程
  		sb.append("hello").append("world").append(true).append(100);
  		
  		System.out.println("sb:"+sb);
  		
  		//public StringBuilder reverse()
  		sb.reverse();
  		System.out.println("sb:"+sb);
  	}
  }
  ```

* String 和 StringBuilder 的互相转换

  ```java
  package com.itheima_03;
  /*
   * StringBuilder和String的相互转换
   * 
   * StringBuilder -- String
   * 		public String toString():通过toString()就可以实现把StringBuilder转成String
   * 
   * String -- StringBuilder
   * 		StringBuilder(String str):通过构造方法就可以实现把String转成StringBuilder
   */
  public class StringBuilderTest {
  	public static void main(String[] args) {
  		//StringBuilder -- String
  		/*
  		StringBuilder sb = new StringBuilder();
  		sb.append("hello").append("world");
  		
  		String s = sb.toString();
  		System.out.println(s);
  		*/
  		
  		//String -- StringBuilder
  		String s = "helloworld";
  		StringBuilder sb = new StringBuilder(s);
  		System.out.println(sb);
  	}
  }
  ```

## day 5 集合类

```java
package com.itheima_01;

import java.util.ArrayList;


/* 集合类的特点：
 * 		长度可变。
 * 
 * ArrayList<E>:
 * 		大小可变数组的实现
 * 
 * 		<E>:是一种特殊的数据类型，泛型。
 * 		怎么用呢?
 * 			在出现E的地方我们使用引用数据类型替换即可
 * 			举例：ArrayList<String>,ArrayList<Student>
 * 
 * 构造方法：
 * 		ArrayList()
 * 
 * 添加元素：
 * 		public boolean add(E e):添加元素
 * 		public void add(int index,E element):在指定的索引处添加一个元素
 */
public class ArrayListDemo {
	public static void main(String[] args) {
		//创建集合对象
		ArrayList<String> array = new  ArrayList<String>();
		
		//add(E e):添加元素
		array.add("hello");
		array.add("world");
		array.add("java");
		
		//add(int index,E element):在指定的索引处添加一个元素
		//array.add(1, "android");
		
		
		System.out.println("array:"+array);
	}
}

```

```java
package com.itheima_01;

import java.util.ArrayList;

/*
 * 获取元素
 * 		public E get(int index):返回指定索引处的元素
 * 集合长度
 * 		public int size():返回集合中的元素的个数
 * 删除元素
 * 		public boolean remove(Object o):删除指定的元素，返回删除是否成功
 * 		public E remove(int index):删除指定索引处的元素，返回被删除的元素
 * 修改元素
 * 		public E set(int index,E element):修改指定索引处的元素，返回被修改的元素
 */
public class ArrayListDemo2 {
	public static void main(String[] args) {
		//创建集合对象
		ArrayList<String> array = new ArrayList<String>();
		
		//添加元素
		array.add("hello");
		array.add("world");
		array.add("java");
		
		//public E get(int index):返回指定索引处的元素
		//System.out.println("get:"+array.get(0));
		//System.out.println("get:"+array.get(1));
		//System.out.println("get:"+array.get(2));
		
		//public int size():返回集合中的元素的个数
		//System.out.println("size:"+array.size());
		
		//public boolean remove(Object o):删除指定的元素，返回删除是否成功
		//System.out.println("remove:"+array.remove("world"));//true
		//System.out.println("remove:"+array.remove("world"));//false
		
		//public E remove(int index):删除指定索引处的元素，返回被删除的元素
		//System.out.println("remove:"+array.remove(0));
		
		//public E set(int index,E element):修改指定索引处的元素，返回被修改的元素
		System.out.println("set:"+array.set(1, "android"));
		
		//输出
		System.out.println("array:"+array);
	}
}
```

## day 6 IO流

#### 基本输入输出流

```java
package com.itheima_01;

import java.io.FileWriter;
import java.io.IOException;

/*
 * 输出流写数据的步骤：
 * 		A:创建输出流对象
 * 		B:调用输出流对象的写数据方法，并刷新缓冲区
 * 		C:释放资源
 * 
 * 相对路径：相对当前项目而言的，在项目的根目录下(a.txt)
 * 绝对路径：以盘符开始的路径(d:\\a.txt)
 * 
 * close()和flush()方法的区别：
 * 		flush():刷新缓冲区。流对象还可以继续使用。
 * 		close():先刷新缓冲区，然后通知系统释放资源。流对象不可以再被使用了。
 */
public class FileWriterDemo2 {
	public static void main(String[] args) throws IOException {
		//创建输出流对象
		//FileWriter fw = new FileWriter("d:\\a.txt");
		FileWriter fw = new FileWriter("a.txt");
		
		//调用输出流对象的写数据方法，并刷新缓冲区
		fw.write("helloworld");
		fw.flush();
		fw.write("java");
		fw.flush();
		
		//释放资源
		fw.close();
		
		//Stream closed
		//fw.write("javaee");
		//fw.flush();
	}
}
```

```java
package com.itheima_01;

import java.io.FileWriter;
import java.io.IOException;

/*
 * 如何实现数据的换行?
 * 		\n可以实现换行，但是windows系统自带的记事本打开并没有换行，这是为什么呢?因为windows识别的换行不是\n，而是\r\n
 * 		windows:\r\n
 * 		linux:\n
 * 		mac:\r
 * 如何实现数据的追加写入?
 * 		FileWriter(String fileName, boolean append)
 */
public class FileWriterDemo4 {
	public static void main(String[] args) throws IOException {
		//创建输出流对象
		//FileWriter fw = new FileWriter("c.txt");
		FileWriter fw = new FileWriter("c.txt",true); //表示追加写入，默认是false
		
		for(int x=0; x<10; x++) {
			fw.write("hello"+x);
			fw.write("\r\n");
		}
		
		//释放资源
		fw.close();
	}
}
```

```java
package com.itheima_02;

import java.io.FileReader;
import java.io.IOException;

/*
 * 输入流读数据的步骤：
 * 		A:创建输入流对象
 * 		B:调用输入流对象的读数据方法
 * 		C:释放资源
 */
public class FileReaderDemo2 {
	public static void main(String[] args) throws IOException {
		//创建输入流对象
		//FileReader fr = new FileReader("fr2.txt");
		FileReader fr = new FileReader("FileWriterDemo.java");
		
		/*
		//调用输入流对象的读数据方法
		//int read(char[] cbuf)：一次读取一个字符数组的数据，返回的是实际读取的字符个数
		char[] chs = new char[5];
		
		//第一次读数据
		int len = fr.read(chs);
		System.out.println("len:"+len);
		//System.out.println(new String(chs));
		System.out.println(new String(chs,0,len));
		
		//第二次读数据
		len = fr.read(chs);
		System.out.println("len:"+len);
		//System.out.println(new String(chs));
		System.out.println(new String(chs,0,len));
		
		//第三次读数据,只读取了四个，上一次读取的第五个字符 r 还保留着
		len = fr.read(chs);
		System.out.println("len:"+len);
		//不要第五个字符 r
		System.out.println(new String(chs,0,len));
		
		//我们发现读取代码的方式相似度很高，想用循环改进，但是不知道什么时候结束循环
		len = fr.read(chs);
		System.out.println("len:"+len);
		len = fr.read(chs);
		System.out.println("len:"+len);
		//通过测试，我们知道，如果实际读取长度是-1的时候，说明没有数据了
		 * 
		 */
		
		//char[] chs = new char[5];
		char[] chs = new char[1024]; //这里可以是1024及其整数倍
		int len;
		//1:fr.read(chs)
		//2:len=fr.read(chs)
		//3:len != -1
		while((len=fr.read(chs))!=-1) {
			//System.out.println(new String(chs,0,len));
			System.out.print(new String(chs,0,len));
		}
		
		//释放资源
		fr.close();
	}
}
```

![FileRead](/assets/blogImg/FileRead.png)

#### 缓冲流

 ```java
package com.itheima_04;

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

/*
 * BufferedWriter:将文本写入字符输出流，缓冲各个字符，从而提供单个字符、数组和字符串的高效写入。 
 * BufferedReader:从字符输入流中读取文本，缓冲各个字符，从而实现字符、数组和行的高效读取。
 */
public class BufferedStreamDemo {
	public static void main(String[] args) throws IOException {
		//创建输出缓冲流对象
		/*
		BufferedWriter bw = new BufferedWriter(new FileWriter("bw.txt"));
		bw.write("hello");
		//bw.flush();
		bw.close();
		*/
		
		//创建输入缓冲流对象
		BufferedReader br = new BufferedReader(new FileReader("FileWriterDemo.java"));
		
		/*
		//一次读写一个字符
		int ch;
		while((ch=br.read())!=-1){
			System.out.print((char)ch);
		}
		*/
		
		//一次读写一个字符数组
		char[] chs = new char[1024];
		int len;
		while((len=br.read(chs))!=-1) {
			System.out.print(new String(chs,0,len));
		}
		
		//释放资源
		br.close();
		
	}
}
 ```

* 缓冲流的特殊功能

```java
package com.itheima_05;

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

/*
 * 缓冲流的特殊功能：
 * BufferedWriter
 * 		void newLine():写一个换行符，这个换行符由系统决定
 * BufferedReader
 * 		String readLine():一次读取一行数据，但是不读取换行符
 */
public class BufferedStreamDemo {
	public static void main(String[] args) throws IOException {
		/*
		BufferedWriter bw = new BufferedWriter(new FileWriter("bw2.txt"));
		for(int x=0; x<10; x++) {
			bw.write("hello"+x);
			//bw.write("\r\n");
			bw.newLine();
			bw.flush();
		}
		bw.close();
		*/
		
		BufferedReader br = new BufferedReader(new FileReader("br.txt"));
		
		/*
		String line = br.readLine();
		System.out.println(line);
		
		line = br.readLine();
		System.out.println(line);
		
		line = br.readLine();
		System.out.println(line);
		
		line = br.readLine();
		System.out.println(line);
		*/
		
		String line;
		//1:br.readLine()
		//2:line=br.readLine()
		//3:line != null
		while((line=br.readLine())!=null) {
			System.out.println(line);
		}
		
		br.close();
	}
}
```

```java
package com.itheima_05;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

/*
 * 需求：
 * 		把项目路径下的FileWriterDemo.java中的内容复制到项目路径下的Copy.java中
 * 
 * 数据源：
 * 		FileWriterDemo.java -- 读数据 -- FileReader -- 高效的读数据 -- BufferedReader
 * 目的地：
 * 		Copy.java -- 写数据 -- FileWriter -- 高效的写数据 -- BufferedWriter
 */
public class CopyFileDemo {
	public static void main(String[] args) throws IOException {
		//创建输入缓冲流对象
		BufferedReader br = new BufferedReader(new FileReader("FileWriterDemo.java"));
		//创建输出缓冲流对象
		BufferedWriter bw = new BufferedWriter(new FileWriter("Copy.java"));
		
		//读写数据
		String line;
		while((line=br.readLine())!=null) {
			bw.write(line);
			bw.newLine();
			bw.flush();
		}
		
		//释放资源
		bw.close();
		br.close();
	}
}
```

```java
【File to ArrayList】
package com.itheima;

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.util.ArrayList;

/*
 * 把上一题的文本文件中的学生信息读取出来存储到集合中，然后遍历集合，在控制台输出
 * 
 * 分析：
 * 		A:定义学生类
 * 		B:创建输入缓冲流对象
 * 		C:创建集合对象
 * 		D:读取文件数据，并把数据按照一定的格式进行分割赋值给学生对象，然后把学生对象作为元素存储到集合
 * 			heima001,向問天,30,北京
 * 		E:释放资源
 * 		F:遍历集合
 */
public class FileToArrayListTest {
	public static void main(String[] args) throws IOException {
		//创建输入缓冲流对象
		BufferedReader br = new BufferedReader(new FileReader("array.txt"));
		
		//创建集合对象
		ArrayList<Student> array = new ArrayList<Student>();
		
		//读取文件数据，并把数据按照一定的格式进行分割赋值给学生对象，然后把学生对象作为元素存储到集合
		String line;
		while((line=br.readLine())!=null) {
			//分割字符串
			String[] strArray = line.split(",");
			//创建学生对象并赋值
			Student s = new Student();
			s.setId(strArray[0]);
			s.setName(strArray[1]);
			s.setAge(strArray[2]);
			s.setAddress(strArray[3]);
			//把学生对象作为元素存储到集合
			array.add(s);
		}
		
		//释放资源
		br.close();
		
		//遍历集合
		System.out.println("学号\t\t姓名\t年龄\t居住地");
		for(int x=0; x<array.size(); x++) {
			Student s = array.get(x);
			System.out.println(s.getId()+"\t"+s.getName()+"\t"+s.getAge()+"\t"+s.getAddress());
		}
	}
}
```

```java
【ArrayList to File】
package com.itheima;

import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Scanner;

/*
 * 键盘录入3个学生信息(学号,姓名,年龄,居住地)存入集合。然后遍历集合把每一个学生信息存入文本文件(每一个学生信息为一行数据，自己定义分割标记)
 * 
 * 分析：
 * 		A:定义学生类
 * 		B:创建集合对象
 * 		C:写方法实现键盘录入学生信息，并把学生对象作为元素添加到集合
 * 		D:创建输出缓冲流对象
 * 		E:遍历集合,得到每一个学生信息，并把学生信息按照一定的格式写入文本文件
 * 			举例：heima001,向问天,30,北京
 * 		F:释放资源
 */
public class ArrayListToFileTest {
	public static void main(String[] args) throws IOException {
		//创建集合对象
		ArrayList<Student> array = new ArrayList<Student>();
		
		//写方法实现键盘录入学生信息，并把学生对象作为元素添加到集合
		addStudent(array);
		addStudent(array);
		addStudent(array);
		
		//创建输出缓冲流对象
		BufferedWriter bw = new BufferedWriter(new FileWriter("array.txt"));
		
		//遍历集合,得到每一个学生信息，并把学生信息按照一定的格式写入文本文件
		for(int x=0; x<array.size(); x++) {
			Student s = array.get(x);
			//heima001,向问天,30,北京
			StringBuilder sb = new StringBuilder();
			sb.append(s.getId()).append(",").append(s.getName()).append(",").append(s.getAge()).append(",").append(s.getAddress());
			
			bw.write(sb.toString());
			bw.newLine();
			bw.flush();
		}
		
		//释放资源
		bw.close();
	}
	
	//添加学生
	public static void addStudent(ArrayList<Student> array) {
		//创建键盘录入对象
		Scanner sc = new Scanner(System.in);
		
		//为了让id能够被访问到，我们就把id定义在了循环的外面
		String id;
		
		//为了让代码能够回到这里，用循环
		while(true) {
			System.out.println("请输入学生学号：");
			//String id = sc.nextLine();
			id = sc.nextLine();
			
			//判断学号有没有被人占用
			//定义标记
			boolean flag = false;
			//遍历集合，得到每一个学生
			for(int x=0; x<array.size(); x++) {
				Student s = array.get(x);
				//获取该学生的学号，和键盘录入的学号进行比较
				if(s.getId().equals(id)) {
					flag = true; //说明学号被占用了
					break;
				}
			}
			
			if(flag) {
				System.out.println("你输入的学号已经被占用,请重新输入");
			}else {
				break; //结束循环
			}
		}
		
		
		System.out.println("请输入学生姓名：");
		String name = sc.nextLine();
		System.out.println("请输入学生年龄：");
		String age = sc.nextLine();
		System.out.println("请输入学生居住地：");
		String address = sc.nextLine();
		
		//创建学生对象
		Student s = new Student();
		s.setId(id);
		s.setName(name);
		s.setAge(age);
		s.setAddress(address);
		
		//把学生对象作为元素添加到集合
		array.add(s);
		
		//给出提示
		System.out.println("添加学生成功");
	}
}
```

* 学生系统，利用文件永久保存信息，利用集合进行增删改查

![StudentSystem](/assets/blogImg/Student.png)

```java
package com.itheima;
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Scanner;

/*
 * 这是我的学生管理系统的主类
 * 
 * 步骤如下：
 * A:定义学生类
 * B:学生管理系统的主界面的代码编写
 * C:学生管理系统的查看所有学生的代码编写
 * D:学生管理系统的添加学生的代码编写
 * E:学生管理系统的删除学生的代码编写
 * F:学生管理系统的修改学生的代码编写
 */
public class StudentManagerTest {
	public static void main(String[] args) throws IOException{
		//定义文件路径
		String fileName = "students.txt";
		
		//为了让程序能够回到这里来，我们使用循环
		while(true) {
			//这是学生管理系统的主界面
			System.out.println("--------欢迎来到学生管理系统--------");
			System.out.println("1 查看所有学生");
			System.out.println("2 添加学生");
			System.out.println("3 删除学生");
			System.out.println("4 修改学生");
			System.out.println("5 退出");
			System.out.println("请输入你的选择：");
			//创建键盘录入对象
			Scanner sc = new Scanner(System.in);
			String choiceString = sc.nextLine();
			//用switch语句实现选择
			switch(choiceString) {
			case "1":
				//查看所有学生
				findAllStudent(fileName);
				break;
			case "2":
				//添加学生
				addStudent(fileName);
				break;
			case "3":
				//删除学生
				deleteStudent(fileName);
				break;
			case "4":
				//修改学生
				updateStudent(fileName);
				break;
			case "5":
			default:
				System.out.println("谢谢你的使用");
				System.exit(0); //JVM退出
				break;
			}
		}
	}
	
	//从文件中读数据到集合
	public static void readData(String fileName,ArrayList<Student> array) throws IOException {
		//创建输入缓冲流对象
		BufferedReader br = new BufferedReader(new FileReader(fileName));
		
		String line;
		while((line=br.readLine())!=null) {
			String[] datas = line.split(",");
			Student s = new Student();
			s.setId(datas[0]);
			s.setName(datas[1]);
			s.setAge(datas[2]);
			s.setAddress(datas[3]);
			array.add(s);
		}
		
		br.close();
	}
	
	//把集合中的数据写入文件
	public static void writeData(String fileName,ArrayList<Student> array) throws IOException {
		//创建输出缓冲流对象
		BufferedWriter bw = new BufferedWriter(new FileWriter(fileName));
		
		for(int x=0; x<array.size(); x++) {
			Student s = array.get(x);
			StringBuilder sb = new StringBuilder();
			sb.append(s.getId()).append(",").append(s.getName()).append(",").append(s.getAge()).append(",").append(s.getAddress());
			
			bw.write(sb.toString());
			bw.newLine();
			bw.flush();
		}
		
		bw.close();
	}
	
	//修改学生
	public static void updateStudent(String fileName) throws IOException {
		//创建集合对象
		ArrayList<Student> array = new ArrayList<Student>();
		//从文件中把数据读取到集合中
		readData(fileName, array);
		
		//修改学生的思路：键盘录入一个学号，到集合中去查找，看是否有学生使用的是该学号，如果有就修改该学生
		//创建键盘录入对象
		Scanner sc = new Scanner(System.in);
		System.out.println("请输入你要修改的学生的学号：");
		String id = sc.nextLine();
		
		//定义一个索引
		int index = -1;
		
		//遍历集合
		for(int x=0; x<array.size(); x++) {
			//获取每一个学生对象
			Student s = array.get(x);
			//拿学生对象的学号和键盘录入的学号进行比较
			if(s.getId().equals(id)) {
				index = x;
				break;
			}
		}
		
		if(index == -1) {
			System.out.println("不好意思,你要修改的学号对应的学生信息不存在,请回去重新你的选择");
		}else {
			System.out.println("请输入学生新姓名：");
			String name = sc.nextLine();
			System.out.println("请输入学生新年龄：");
			String age = sc.nextLine();
			System.out.println("请输入学生新居住地：");
			String address = sc.nextLine();
			
			//创建学生对象
			Student s = new Student();
			s.setId(id);
			s.setName(name);
			s.setAge(age);
			s.setAddress(address);
			
			//修改集合中的学生对象
			array.set(index, s);
			//把集合中的数据重新写回到文件
			writeData(fileName, array);
			//给出提示
			System.out.println("修改学生成功");
		}
	}
	
	//删除学生
	public static void deleteStudent(String fileName) throws IOException {
		//创建集合对象
		ArrayList<Student> array = new ArrayList<Student>();
		//从文件中把数据读取到集合中
		readData(fileName, array);
		
		//删除学生的思路：键盘录入一个学号，到集合中去查找，看是否有学生使用的是该学号，如果有就删除该学生
		//创建键盘录入对象
		Scanner sc = new Scanner(System.in);
		System.out.println("请输入你要删除的学生的学号：");
		String id = sc.nextLine();
		
		//我们必须给出学号不存在的时候的提示
		
		//定义一个索引
		int index = -1;
		
		//遍历集合
		for(int x=0; x<array.size(); x++) {
			//获取到每一个学生对象
			Student s = array.get(x);
			//拿这个学生对象的学号和键盘录入的学号进行比较
			if(s.getId().equals(id)) {
				index = x;
				break;
			}
		}
		
		if(index == -1) {
			System.out.println("不好意思,你要删除的学号对应的学生信息不存在,请回去重新你的选择");
		}else {
			array.remove(index);
			//把集合中的数据重新写回到文件
			writeData(fileName, array);
			System.out.println("删除学生成功");
		}
		
	}
	
	//添加学生
	public static void addStudent(String fileName) throws IOException {
		//创建集合对象
		ArrayList<Student> array = new ArrayList<Student>();
		//从文件中把数据读取到集合中
		readData(fileName, array);
				
		//创建键盘录入对象
		Scanner sc = new Scanner(System.in);
		
		//为了让id能够被访问到，我们就把id定义在了循环的外面
		String id;
		
		//为了让代码能够回到这里，用循环
		while(true) {
			System.out.println("请输入学生学号：");
			//String id = sc.nextLine();
			id = sc.nextLine();
			
			//判断学号有没有被人占用
			//定义标记
			boolean flag = false;
			//遍历集合，得到每一个学生
			for(int x=0; x<array.size(); x++) {
				Student s = array.get(x);
				//获取该学生的学号，和键盘录入的学号进行比较
				if(s.getId().equals(id)) {
					flag = true; //说明学号被占用了
					break;
				}
			}
			
			if(flag) {
				System.out.println("你输入的学号已经被占用,请重新输入");
			}else {
				break; //结束循环
			}
		}
		
		
		System.out.println("请输入学生姓名：");
		String name = sc.nextLine();
		System.out.println("请输入学生年龄：");
		String age = sc.nextLine();
		System.out.println("请输入学生居住地：");
		String address = sc.nextLine();
		
		//创建学生对象
		Student s = new Student();
		s.setId(id);
		s.setName(name);
		s.setAge(age);
		s.setAddress(address);
		
		//把学生对象作为元素添加到集合
		array.add(s);
		//把集合中的数据重新写回到文件
		writeData(fileName, array);
		
		//给出提示
		System.out.println("添加学生成功");
	}
	
	//查看所有学生
	public static void findAllStudent(String fileName) throws IOException {
		//创建集合对象
		ArrayList<Student> array = new ArrayList<Student>();
		//从文件中把数据读取到集合中
		readData(fileName, array);
		
		//首先来判断集合中是否有数据，如果没有数据，就给出提示，并让该方法不继续往下执行
		if(array.size() == 0) {
			System.out.println("不好意思,目前没有学生信息可供查询,请回去重新选择你的操作");
			return;
		}
		
		//\t 其实就是一个tab键的位置
		System.out.println("学号\t\t姓名\t年龄\t居住地");
		for(int x=0; x<array.size(); x++) {
			Student s = array.get(x);
			System.out.println(s.getId()+"\t"+s.getName()+"\t"+s.getAge()+"\t"+s.getAddress());
		}
	}
}
```

## day 7 静态——与类相关

```java
package com.itheima_04;

/*
 *   Coder静态代码块执行 --- Coder构造代码块执行 --- Coder无参空构造执行
 *   
 *   
 *   BlockTest静态代码块执行 --- BlockTest的主函数执行了 --- Coder静态代码块执行 --- Coder构造代码块执行 --- Coder无参空构造执行
 *   Coder构造代码块执行 --- Coder无参空构造执行
 * 
 */
public class BlockTest {
	static {
		System.out.println("BlockTest静态代码块执行");
	}
	
	{
		System.out.println("BlockTest构造代码块执行");
	}
	

	public BlockTest(){
		System.out.println("BlockTest无参构造执行了");
	}
	
	public static void main(String[] args) {
		System.out.println("BlockTest的主函数执行了");
		Coder c = new Coder();
		Coder c2 = new Coder();
	}
}

class Coder {
	
	static {
		System.out.println("Coder静态代码块执行");
	}
	
	{
		System.out.println("Coder构造代码块执行");
	}
	
	public Coder() {
		System.out.println("Coder无参空构造执行");
	}	
	
}
```

* 静态代码块在类创建的时候执行；构造代码块在 new 的时候执行，且先于构造方法执行