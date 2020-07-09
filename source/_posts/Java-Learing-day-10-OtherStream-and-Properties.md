---
title: Java：字节流 & 递归
date: 2020-03-03 20:20:36
categories: 
toc: true
tags: [Java]
---

###序列流（了解）

* 1.什么是序列流
	* 序列流可以把多个字节输入流整合成一个, 从序列流中读取数据时, 将从被整合的第一个流开始读, 读完一个之后继续读第二个, 以此类推.

<!-- more -->

* 2.使用方式
	
	* 整合两个: SequenceInputStream(InputStream, InputStream)
	
	```java
	package com.heima.otherio;
	
	import java.io.FileInputStream;
	import java.io.FileNotFoundException;
	import java.io.FileOutputStream;
	import java.io.IOException;
	import java.io.SequenceInputStream;
	import java.util.Enumeration;
	import java.util.Vector;
	
	public class Demo01_SequenceInputStream {
	
		/**
		 * @param args
		 * 整合两个输入流
		 * SequenceInputStream(InputStream s1, InputStream s2)
		 * 整合多个输入流
		 * SequenceInputStream(Enumeration<? extends InputStream> e)
		 * @throws IOException 
		 */
		public static void main(String[] args) throws IOException {
			//demo1();
			//demo2();
			FileInputStream fis1 = new FileInputStream("a.txt");
			FileInputStream fis2 = new FileInputStream("b.txt");
			FileInputStream fis3 = new FileInputStream("c.txt");
			
			Vector<FileInputStream> v = new Vector<>();					//创建集合对象
			v.add(fis1);												//将流对象存储进来
			v.add(fis2);
			v.add(fis3);
			
			Enumeration<FileInputStream> en = v.elements();
			SequenceInputStream sis = new SequenceInputStream(en);		//将枚举中的输入流整合成一个
			FileOutputStream fos = new FileOutputStream("d.txt");
			
			int b;
			while((b = sis.read()) != -1) {
				fos.write(b);
			}
			
			sis.close();
			fos.close();
		}
	
		public static void demo2() throws FileNotFoundException, IOException {
			FileInputStream fis1 = new FileInputStream("a.txt");
			FileInputStream fis2 = new FileInputStream("b.txt");
			SequenceInputStream sis = new SequenceInputStream(fis1, fis2);
			FileOutputStream fos = new FileOutputStream("c.txt");
			
			int b;
			while((b = sis.read()) != -1) {
				fos.write(b);
			}
			
			sis.close();					//sis在关闭的时候,会将构造方法中传入的流对象也都关闭
			fos.close();
		}
	
		public static void demo1() throws FileNotFoundException, IOException {
			FileInputStream fis1 = new FileInputStream("a.txt");		//创建字节输入流关联a.txt
			FileOutputStream fos = new FileOutputStream("c.txt");		//创建字节输出流关联c.txt
			
			int b1;
			while((b1 = fis1.read()) != -1) {							//不断的在a.txt上读取字节
				fos.write(b1);											//将读到的字节写到c.txt上
			}
			fis1.close();												//关闭字节输入流
			
			FileInputStream fis2 = new FileInputStream("b.txt");
			int b2;
			while((b2 = fis2.read()) != -1) {
				fos.write(b2); 
			}
			
			fis2.close();
			fos.close();
		}
	}
```
	###内存输出流（掌握）
* 1.什么是内存输出流
	
	* 该输出流可以向内存中写数据, 把内存当作一个缓冲区, 写出之后可以一次性获取出所有数据
	
* 2.使用方式
	
	* 创建对象: new ByteArrayOutputStream()
	* 写出数据: write(int), write(byte[])
	* 获取数据: toByteArray()
	
	```java
	package com.heima.otherio;
	
	import java.io.ByteArrayOutputStream;
	import java.io.FileInputStream;
	import java.io.FileNotFoundException;
	import java.io.IOException;
	
	public class Demo02_ByteArrayOutputStream {
	
		/**
		 * @param args
		 * ByteArrayOutputStream
		 * 内存输出流
		 * 
		 * FileInputStream读取中文的时候出现了乱码
		 * 
		 * 解决方案
		 * 1,字符流读取
		 * 2,ByteArrayOutputStream
		 * @throws IOException 
		 */
		public static void main(String[] args) throws IOException {
			//demo1();
			FileInputStream fis = new FileInputStream("e.txt");
			ByteArrayOutputStream baos = new ByteArrayOutputStream();			//在内存中创建了可以增长的内存数组
			
			int b;
			while((b = fis.read()) != -1) {
				baos.write(b);													//将读取到的数据逐个写到内存中
			}
			
			//byte[] arr = baos.toByteArray();									//将缓冲区的数据全部获取出来,并赋值给arr数组
			//System.out.println(new String(arr));              
			
			System.out.println(baos.toString());								//将缓冲区的内容转换为了字符串,在输出语句中可以省略调用toString方法
			fis.close();
		}
	
		public static void demo1() throws FileNotFoundException, IOException {
			FileInputStream fis = new FileInputStream("e.txt");
			byte[] arr = new byte[3];
			int len;
			while((len = fis.read(arr)) != -1) {
				System.out.println(new String(arr,0,len));
			}
			
			fis.close();
		}
	}
	```
###内存输出流之面试题（掌握）
* 定义一个文件输入流,调用read(byte[] b)方法,将a.txt文件中的内容打印出来(byte数组大小限制为5)

    ```java
    package com.heima.test;
    
    import java.io.ByteArrayOutputStream;
    import java.io.FileInputStream;
    import java.io.FileNotFoundException;
    import java.io.IOException;
    
    public class Test1 {
    
    	/**
    	 * @param args
    	 * 定义一个文件输入流,调用read(byte[] b)方法,将a.txt文件中的内容打印出来(byte数组大小限制为5)
    	 * 
    	 * 分析:
    	 * 1,reda(byte[] b)是字节输入流的方法,创建FileInputStream,关联a.txt
    	 * 2,创建内存输出流,将读到的数据写到内存输出流中
    	 * 3,创建字节数组,长度为5
    	 * 4,将内存输出流的数据全部转换为字符串打印
    	 * 5,关闭输入流
    	 * @throws IOException 
    	 */
    	public static void main(String[] args) throws IOException {
    		//1,reda(byte[] b)是字节输入流的方法,创建FileInputStream,关联a.txt
    		FileInputStream fis = new FileInputStream("a.txt");
    		//2,创建内存输出流,将读到的数据写到内存输出流中
    		ByteArrayOutputStream baos = new ByteArrayOutputStream();
    		//3,创建字节数组,长度为5
    		byte[] arr = new byte[5];
    		int len;
    		
    		while((len = fis.read(arr)) != -1) {
    			baos.write(arr, 0, len);
    			//System.out.println(new String(arr,0,len));    乱码
    		}
    		//4,将内存输出流的数据全部转换为字符串打印
    		System.out.println(baos); 					//即使没有调用,底层也会默认帮我们调用toString()方法
    		//5,关闭输入流
    		fis.close();
    	}
    }
    ```
###随机访问流概述和读写数据（了解）
* A:随机访问流概述
	* RandomAccessFile概述
	* RandomAccessFile类不属于流，是Object类的子类。但它融合了InputStream和OutputStream的功能。
	* 支持对随机访问文件的读取和写入。

* B:read(),write(),seek()
	
	```java
	package com.heima.otherio;
	
	import java.io.FileNotFoundException;
	import java.io.IOException;
	import java.io.RandomAccessFile;
	
	public class Demo08_RandomAccessFile {
	
		/**
		 * @param args
		 * @throws IOException 
		 */
		public static void main(String[] args) throws IOException {
			RandomAccessFile raf = new RandomAccessFile("g.txt", "rw");
			//raf.write(97);
			//int x = raf.read();
			//System.out.println(x);
			raf.seek(0);					//在指定位置设置指针
			raf.write(98);
			raf.close();
		}
	}
	```
###对象操作流 ObjecOutputStream（了解）
* 1.什么是对象操作流
	
	* 该流可以将一个对象写出, 或者读取一个对象到程序中. 也就是执行了序列化和反序列化的操作.
	
* 2.使用方式
	
	```java
	[Person.java]
	package com.heima.bean;
	
	import java.io.Serializable;
	
	public class Person implements Serializable {
		/**
		 * 
		 */
		private static final long serialVersionUID = 2L;  //版本号，加了更好控制版本和识别报错。可不加 
		private String name;
		private int age;
		
		public Person() {
			super();
			
		}
		public Person(String name, int age) {
			super();
			this.name = name;
			this.age = age;
		}
		public String getName() {
			return name;
		}
		public void setName(String name) {
			this.name = name;
		}
		public int getAge() {
			return age;
		}
		public void setAge(int age) {
			this.age = age;
		}
		@Override
		public String toString() {
			return "Person [name=" + name + ", age=" + age + "]";
		}	
	}
	
	
	[Demo03_ObjectOutputStream.java]
	package com.heima.otherio;
	
	import java.io.FileNotFoundException;
	import java.io.FileOutputStream;
	import java.io.IOException;
	import java.io.ObjectOutputStream;
	import java.util.ArrayList;
	
	import com.heima.bean.Person;
	
	public class Demo03_ObjectOutputStream {
	
		/**
		 * @param args
		 * 序列化:将对象写到文件上
		 * @throws IOException 
		 */
		public static void main(String[] args) throws IOException {
			//demo1();
			Person p1 = new Person("张三", 23);
			Person p2 = new Person("李四", 24);
			Person p3 = new Person("王五", 25);
			Person p4 = new Person("赵六", 26);
			
			ArrayList<Person> list = new ArrayList<>();
			list.add(p1);
			list.add(p2);
			list.add(p3);
			list.add(p4);
			
			ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("e.txt"));
			oos.writeObject(list);								//把整个集合对象一次写出
			oos.close();
		}
	
		public static void demo1() throws IOException, FileNotFoundException {
			Person p1 = new Person("张三", 23);
			Person p2 = new Person("李四", 24);
			
			ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("e.txt"));
			oos.writeObject(p1);
			oos.writeObject(p2);
			
			oos.close();
		}
	}
	```
###对象操作流 ObjectInputStream
* 读取: new ObjectInputStream(InputStream), readObject()

```java
package com.heima.otherio;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.util.ArrayList;

import com.heima.bean.Person;

public class Demo04_ObjectInputStream {

	/**
	 * @param args
	 * @throws IOException 
	 * @throws FileNotFoundException 
	 * @throws ClassNotFoundException 
	 * ObjectInputStream
	 * 对象输入流,反序列化
	 */
	public static void main(String[] args) throws FileNotFoundException, IOException, ClassNotFoundException {
		//demo1();
		ObjectInputStream ois = new ObjectInputStream(new FileInputStream("e.txt"));
		ArrayList<Person> list = (ArrayList<Person>) ois.readObject();		//将集合对象一次读取
		
		for (Person person : list) {
			System.out.println(person);
		}
		
		ois.close();
	}

	public static void demo1() throws IOException, FileNotFoundException,
			ClassNotFoundException {
		ObjectInputStream ois = new ObjectInputStream(new FileInputStream("e.txt"));
		
		Person p1 = (Person) ois.readObject();
		Person p2 = (Person) ois.readObject();
		//Person p3 = (Person) ois.readObject();			//当文件读取到了末尾时出现EOFException
		
		System.out.println(p1);
		System.out.println(p2);
		
		ois.close();
	}
}
```

###数据输入输出流（了解）
* 1.什么是数据输入输出流
	
	* DataInputStream, DataOutputStream可以按照基本数据类型大小读写数据
	* 例如按Long大小写出一个数字, 写出时该数据占8字节. 读取的时候也可以按照Long类型读取, 一次读取8个字节.
	
* 2.使用方式

    ```java
    package com.heima.otherio;
    
    import java.io.DataInputStream;
    import java.io.DataOutputStream;
    import java.io.FileInputStream;
    import java.io.FileNotFoundException;
    import java.io.FileOutputStream;
    import java.io.IOException;
    
    public class Demo09_Data {
    
    	/**
    	 * @param args
    	 * @throws IOException 
    	 * 00000000 00000000 00000011 11100101	int类型997
    	 * 11100101
    	 * 00000000 00000000 00000000 11100101
    	 */
    	public static void main(String[] args) throws IOException {
    		//demo1();
    		//demo2();
    		//demo3();
    		DataInputStream dis = new DataInputStream(new FileInputStream("h.txt"));
    		int x = dis.readInt();
    		int y = dis.readInt();
    		int z = dis.readInt();
    		
    		System.out.println(x);
    		System.out.println(y);
    		System.out.println(z);
    		
    		dis.close();
    	}
    
    	public static void demo3() throws FileNotFoundException, IOException {
    		DataOutputStream dos = new DataOutputStream(new FileOutputStream("h.txt"));
    		dos.writeInt(997);
    		dos.writeInt(998);
    		dos.writeInt(999);
    		dos.close();
    	}
    
    	public static void demo2() throws FileNotFoundException, IOException {
    		FileInputStream fis = new FileInputStream("h.txt");
    		int x = fis.read();
    		int y = fis.read();
    		int z = fis.read();
    		
    		System.out.println(x);
    		System.out.println(y);
    		System.out.println(z);
    		
    		fis.close();
    	}
    
    	public static void demo1() throws FileNotFoundException, IOException {
    		FileOutputStream fos = new FileOutputStream("h.txt");
    		fos.write(997);
    		fos.write(998);
    		fos.write(999);
    		
    		fos.close();
    	}
    }
    ```

###打印流的概述和特点（掌握）
* 1.什么是打印流
	
	* 该流可以很方便的将对象的toString()结果输出, 并且自动加上换行, 而且可以使用自动刷出的模式
* System.out就是一个**PrintStream**, 其默认向控制台输出信息
	
* 2.使用方式
	
	* 打印: print(), println()
	* **自动刷出**: **PrintWriter**(OutputStream out, boolean autoFlush, String encoding) 
* 打印流只操作数据目的
	
	```java
	package com.heima.otherio;
	
	import java.io.FileNotFoundException;
	import java.io.FileOutputStream;
	import java.io.IOException;
	import java.io.PrintStream;
	import java.io.PrintWriter;
	
	import com.heima.bean.Person;
	
	public class Demo05_PrintStream {
	
		/**
		 * @param args
		 * @throws IOException 
		 * PrintStream和PrintWriter分别是打印的字节流和字符流
		 * 只操作数据目的的
		 */
		public static void main(String[] args) throws IOException {
			//demo1();
			PrintWriter pw = new PrintWriter(new FileOutputStream("f.txt"),true);
			//pw.println(97);							//自动刷出功能只针对的是println方法
			//pw.write(97);
			pw.print(97);
			pw.println(97);               //由于println()自动刷出功能，上面的print也会写入
			pw.close();                   //由于关闭流会刷新缓冲，自动刷出没啥用
		}
	
		public static void demo1() {
			System.out.println("aaa");
			PrintStream ps = System.out;			//获取标注输出流
			ps.println(97);							//底层通过Integer.toString()将97转换成字符串并打印
			ps.write(97);							//查找码表,找到对应的a并打印
			
			Person p1 = new Person("张三", 23);
			ps.println(p1);							//默认调用p1的toString方法
			
			Person p2 = null;						//打印引用数据类型,如果是null,就打印null,如果不是null就打印对象的toString方法
			ps.println(p2);
			ps.close();
		}
	}
	```

###标准输入输出流概述和输出语句
* 1.什么是标准输入输出流(掌握)
	* System.in是InputStream, 标准输入流, 默认可以从键盘输入读取字节数据
	* System.out是PrintStream, 标准输出流, 默认可以向Console中输出字符和字节数据
	
* 2.修改标准输入输出流(了解)
	* 修改输入流: System.setIn(InputStream)
	* 修改输出流: System.setOut(PrintStream)
	
	```java
	package com.heima.otherio;
	
	import java.io.FileInputStream;
	import java.io.IOException;
	import java.io.InputStream;
	import java.io.PrintStream;
	
	public class Demo06_SystemInOut {
	
		/**
		 * @param args
		 * @throws IOException 
		 */
		public static void main(String[] args) throws IOException {
			//demo1();
			System.setIn(new FileInputStream("a.txt"));			//改变标准输入流
			System.setOut(new PrintStream("b.txt"));			//改变标注输出流
			
			InputStream is = System.in;							//获取标准的键盘输入流,默认指向键盘,改变后指向文件
			PrintStream ps = System.out;						//获取标准输出流,默认指向的是控制台,改变后就指向文件
			
			int b;
			while((b = is.read()) != -1) {
				ps.write(b);
			}
			//System.out.println();								//也是一个输出流,不用关,因为没有和硬盘上的文件产生关联的管道
			is.close();
			ps.close();
			
		}
	
		public static void demo1() throws IOException {
			InputStream is = System.in;
			int x = is.read();
			System.out.println(x);
			
			is.close();
			
			InputStream is2 = System.in;
			int y = is2.read();
			System.out.println(y);
		}
	}
	```

###修改标准输入输出流拷贝图片（了解）

```java
package com.heima.test;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;
import java.io.PrintStream;

public class Test2 {

	/**
	 * @param args
	 * @throws IOException 
	 */
	public static void main(String[] args) throws IOException {
		System.setIn(new FileInputStream("双元.jpg"));			//改变标准输入流
		System.setOut(new PrintStream("copy.jpg")); 			//改变标准输出流
		
		InputStream is = System.in;
		PrintStream ps = System.out;
		
		byte[] arr = new byte[1024];
		int len;
		
		while((len = is.read(arr)) != -1) {
			ps.write(arr, 0, len);
		}
		
		is.close();
		ps.close();
	}
}
```

###两种方式实现键盘录入（了解）
* A:BufferedReader的readLine方法。
	* BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
* B:Scanner

```java
package com.heima.otherio;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Scanner;

public class Demo07_SystemIn {

	/**
	 * @param args
	 * @throws IOException 
	 */
	public static void main(String[] args) throws IOException {
		/*BufferedReader br = new BufferedReader(new InputStreamReader(System.in));		//InputStreamReader转换流
		String line = br.readLine();
		System.out.println(line);
		br.close();*/
		
		Scanner sc = new Scanner(System.in);
		String line = sc.nextLine();
		System.out.println(line);
		sc.close();  //可以不关，因为System.in没有和硬盘文件产生关联
	}
}
```


###Properties的概述和作为Map集合的使用（了解）
* A:Properties的概述
	* Properties 类表示了一个持久的属性集。
	* Properties 可保存在流中或从流中加载。
	* 属性列表中每个键及其对应值都是一个字符串。 
* B:案例演示
	* Properties作为Map集合的使用
	

###Properties的特殊功能使用（了解）
* A:Properties的特殊功能
	* public Object setProperty(String key,String value)
	* public String getProperty(String key)
	* public Enumeration<String> stringPropertyNames()
	
* B:案例演示
	* Properties的特殊功能
	
	```java
	package com.heima.otherio;
	
	import java.io.FileInputStream;
	import java.io.FileNotFoundException;
	import java.io.FileOutputStream;
	import java.io.IOException;
	import java.util.Enumeration;
	import java.util.Properties;
	
	public class Demo10_Properties {
	
		/**
		 * @param args
		 * Properties是Hashtable的子类
		 * @throws IOException 
		 * @throws FileNotFoundException 
		 */
		public static void main(String[] args) throws FileNotFoundException, IOException {
			//demo1();
			//demo2();
			Properties prop = new Properties();
			prop.load(new FileInputStream("config.properties"));		//将文件上的键值对读取到集合中
			prop.setProperty("tel", "18912345678");
			prop.store(new FileOutputStream("config.properties"), null);//第二个参数是对列表参数的描述,可以给值,也可以给null
			System.out.println(prop);
		}
	
		public static void demo2() {
			Properties prop = new Properties();
			prop.setProperty("name", "张三");
			prop.setProperty("tel", "18912345678");
			
			//System.out.println(prop);
			Enumeration<String> en = (Enumeration<String>) prop.propertyNames();
			while(en.hasMoreElements()) {
				String key = en.nextElement();				//获取Properties中的每一个键
				String value = prop.getProperty(key);		//根据键获取值
				System.out.println(key + "="+ value);
			}
		}
	
		public static void demo1() {
			Properties prop = new Properties();
			prop.put("abc", 123);
			System.out.println(prop);
		}
	}
	```
###Properties的load()和store()功能（了解）
* A:Properties的load()和store()功能

* B:案例演示
	* Properties的load()和store()功能
	
	```java
	package com.heima.otherio;
	
	import java.io.FileInputStream;
	import java.io.FileNotFoundException;
	import java.io.FileOutputStream;
	import java.io.IOException;
	import java.util.Enumeration;
	import java.util.Properties;
	
	public class Demo10_Properties {
	
		/**
		 * @param args
		 * Properties是Hashtable的子类
		 * @throws IOException 
		 * @throws FileNotFoundException 
		 */
		public static void main(String[] args) throws FileNotFoundException, IOException {
			//demo1();
			//demo2();
			Properties prop = new Properties();
			prop.load(new FileInputStream("config.properties"));		//将文件上的键值对读取到集合中
			prop.setProperty("tel", "18912345678");
			prop.store(new FileOutputStream("config.properties"), null);//第二个参数是对列表参数的描述,可以给值,也可以给null
			System.out.println(prop);
		}
	
		public static void demo2() {
			Properties prop = new Properties();
			prop.setProperty("name", "张三");
			prop.setProperty("tel", "18912345678");
			
			//System.out.println(prop);
			Enumeration<String> en = (Enumeration<String>) prop.propertyNames();
			while(en.hasMoreElements()) {
				String key = en.nextElement();				//获取Properties中的每一个键
				String value = prop.getProperty(key);		//根据键获取值
				System.out.println(key + "="+ value);
			}
		}
	
		public static void demo1() {
			Properties prop = new Properties();
			prop.put("abc", 123);
			System.out.println(prop);
		}
	}
	```
