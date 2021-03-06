---
title: Java：IO流
date: 2020-03-01 12:52:36
categories: 
toc: true
tags: [Java]
---

### IO流概述及其分类

* 1.概念
	* IO流用来处理设备之间的数据传输
	* Java对数据的操作是通过流的方式
	* Java用于操作流的类都在IO包中
	* 流按流向分为两种：输入流，输出流。
	* 流按操作类型分为两种：
		* 字节流 : 字节流可以操作任何数据,因为在计算机中任何数据都是以字节的形式存储的
		* 字符流 : 字符流只能操作纯字符数据，比较方便。
* 2.IO流常用父类
	* 字节流的抽象父类：
		* InputStream 
		* OutputStream
	* 字符流的抽象父类：
		* Reader 
		* Writer		
* 3.IO程序书写
	* 使用前，导入IO包中的类
	* 使用时，进行IO异常处理
	* 使用后，释放资源

<!-- more -->

### FileInputStream

* read()一次读取一个字节

   ```java
   package com.heima.stream;
   
   import java.io.FileInputStream;
   import java.io.FileNotFoundException;
   import java.io.IOException;
   
   public class Demo1_FileInputStream {
   
   	/**
   	 * @param args
   	 * @throws IOException  
   	 * read()方法读取的是一个字节,为什么返回是int,而不是byte
   	 * 因为字节输入流可以操作任意类型的文件,比如图片音频等,这些文件底层都是以二进制形式的存储的,如果每次读取都返回byte,有可能在读到中间的时候遇到111111111。
   	 * 那么这11111111是byte类型的-1,我们的程序是遇到-1就会停止不读了,后面的数据就读不到了,所以在读取的时候用int类型接收,如果11111111会在其前面补上24个0凑足4个字节,那么byte类型的-1就变成int类型的255了。
   	 * 这样可以保证整个数据读完,而结束标记的-1就是int类型。当写入时，前面的添补位会恢复原样。 
   	 *
   	 * 00010100 00100100 01000001 11111111(如果返回byte，读取就结束了) 00000000
   	 * 
   	 * 10000001    byte类型-1的原码
   	 * 11111110	   -1的反码
   	 * 11111111    -1的补码
   	 * 
   	 * 00000000 00000000 00000000 11111111
   	 */
   	public static void main(String[] args) throws IOException {
   		//demo1();
   		FileInputStream fis = new FileInputStream("xxx.txt");	//创建流对象
   		int b;
   		while((b = fis.read()) != -1) {
   			System.out.println(b);
   		}
   		
   		fis.close();
   	}
   
   	public static void demo1() throws FileNotFoundException, IOException {
   		FileInputStream fis = new FileInputStream("xxx.txt");	//创建流对象
   		int x = fis.read();										//从硬盘上读取一个字节
   		System.out.println(x);
   		int y = fis.read();
   		System.out.println(y);
   		int z = fis.read();
   		System.out.println(z);
   		int a = fis.read();
   		System.out.println(a);
   		int b = fis.read();
   		System.out.println(b);
   		fis.close();											//关流释放资源
   	}
   }
   ```



### FileOutputStream

* write()一次写出一个字节

   ```java
   package com.heima.stream;
   
   import java.io.FileNotFoundException;
   import java.io.FileOutputStream;
   import java.io.IOException;
   
   public class Demo2_FileOutputStream {
   
   	/**
   	 * @param args
   	 * @throws IOException 
   	 * FileOutputStream在创建对象的时候是如果没有这个文件会帮我创建出来
   	 * 如果有这个文件就会先将文件清空
   	 */
   	public static void main(String[] args) throws IOException {
   		//demo1();
   		FileOutputStream fos = new FileOutputStream("yyy.txt",true);	//如果想续写就在第二个追加参数传true
   		fos.write(97);
   		fos.write(98);
   		
   		fos.close();
   	}
   
   	public static void demo1() throws FileNotFoundException, IOException {
   		FileOutputStream fos = new FileOutputStream("yyy.txt");		//创建字节输出流对象,如果没有就自动创建一个
   		//fos.write(97);				//虽然写出的是一个int数,但是到文件上的是一个字节,会自动去除前三个8位
   		//fos.write(98);
   		//fos.write(99);
   		fos.write(100);
   		fos.close();
   	}
   }
   ```

### 拷贝音频

* FileInputStream读取

  ```java
  package com.heima.stream;
  
  import java.io.FileInputStream;
  import java.io.FileNotFoundException;
  import java.io.FileOutputStream;
  import java.io.IOException;
  
  public class Demo3_Copy {
  
  	/**
  	 * @param args
  	 * @throws IOException 
  	 */
  	public static void main(String[] args) throws IOException {
  		//demo1(); 逐字节拷贝，效率太低
  		//demo2();
  		//demo3();
  	}
  
  	public static void demo3() throws FileNotFoundException, IOException {
  		//第二种拷贝,不推荐使用,因为有可能会导致内存溢出
  		FileInputStream fis = new FileInputStream("致青春.mp3");		//创建输入流对象,关联致青春.mp3
  		FileOutputStream fos = new FileOutputStream("copy.mp3");	//创建输出流对象,关联copy.mp3
  		//int len = fis.available();
  		//System.out.println(len);
  		
  		byte[] arr = new byte[fis.available()];						//创建与文件一样大小的字节数组
  		fis.read(arr);												//将文件上的字节读取到内存中
  		fos.write(arr);												//将字节数组中的字节数据写到文件上
  		
  		fis.close();
  		fos.close();
  	}
  
  	public static void demo2() throws FileNotFoundException, IOException {
  		FileInputStream fis = new FileInputStream("致青春.mp3");		//创建输入流对象,关联致青春.mp3
  		FileOutputStream fos = new FileOutputStream("copy.mp3");	//创建输出流对象,关联copy.mp3
  		
  		int b;
  		while((b = fis.read()) != -1) {								//在不断的读取每一个字节
  			fos.write(b);											//将每一个字节写出
  		}
  		
  		fis.close();												//关流释放资源
  		fos.close();
  	}
  
  	public static void demo1() throws FileNotFoundException, IOException {
  		FileInputStream fis = new FileInputStream("双元.jpg");		//创建输入流对象,关联双元.jpg
  		FileOutputStream fos = new FileOutputStream("copy.jpg");	//创建输出流对象,关联copy.jpg
  		
  		int b;
  		while((b = fis.read()) != -1) {								//在不断的读取每一个字节
  			fos.write(b);											//将每一个字节写出
  		}
  		
  		fis.close();												//关流释放资源
  		fos.close();
  	}
  }
  ```

### 字节数组拷贝之available()方法

* A:案例演示（见上面拷贝音频代码第二种方法）
	* int read(byte[] b):一次读取一个字节数组
	* write(byte[] b):一次写出一个字节数组
	* available()获取读的文件所有的字节个数
### 定义小数组
* write(byte[] b)

* write(byte[] b, int off, int len)写出有效的字节个数
	
	```java
	package com.heima.stream;
	
	import java.io.FileInputStream;
	import java.io.FileNotFoundException;
	import java.io.FileOutputStream;
	import java.io.IOException;
	
	public class Demo4_ArrayCopy {
	
		/**
		 * @param args
		 * 第三种拷贝
		 * 定义小数组
		 * @throws IOException 
		 */
		public static void main(String[] args) throws IOException {
			//demo1();
			//demo2();
			FileInputStream fis = new FileInputStream("致青春.mp3");
			FileOutputStream fos = new FileOutputStream("copy.mp3");
			
			byte[] arr = new byte[1024 * 8];
			int len;
			while((len = fis.read(arr)) != -1) {				//如果忘记加arr,返回的就不是读取的字节个数,而是字节的码表值
				fos.write(arr,0,len);
			}
			
			fis.close();
			fos.close();
		}
	
		public static void demo2() throws FileNotFoundException, IOException {
			FileInputStream fis = new FileInputStream("xxx.txt");
			FileOutputStream fos = new FileOutputStream("yyy.txt");
			
			byte[] arr = new byte[2];
			int len;
			while((len = fis.read(arr)) != -1) {
				fos.write(arr,0,len);
			}
			
			fis.close();
			fos.close();
		}
	
		public static void demo1() throws FileNotFoundException, IOException {
			FileInputStream fis = new FileInputStream("xxx.txt");
			byte[] arr = new byte[2];
			int a = fis.read(arr);						//将文件上的字节读取到字节数组中
			
			System.out.println(a);						//读到的有效字节个数
			for (byte b : arr) {						//第一次获取到文件上的a和b
				System.out.println(b);
			}
			System.out.println("-----------------------");
			int c = fis.read(arr);
			System.out.println(c);
			for (byte b : arr) {
				System.out.println(b);
			}
			fis.close();
		}
	}
	```

### BufferedInputStream和BufferOutputStream拷贝

* A:缓冲思想
	* 字节流一次读写一个数组的速度明显比一次读写一个字节的速度快很多，
	* 这是加入了数组这样的缓冲区效果，java本身在设计的时候，
	* 也考虑到了这样的设计思想(装饰设计模式后面讲解)，所以提供了字节缓冲区流
* B.BufferedInputStream
	* BufferedInputStream内置了一个缓冲区(数组)
	* 从BufferedInputStream中读取一个字节时
	* BufferedInputStream会一次性从文件中读取8192个, 存在缓冲区中, 返回给程序一个
	* 程序再次读取时, 就不用找文件了, 直接从缓冲区中获取
	* 直到缓冲区中所有的都被使用过, 才重新从文件中读取8192个
* C.BufferedOutputStream
	* BufferedOutputStream也内置了一个缓冲区(数组)
	* 程序向流中写出字节时, 不会直接写到文件, 先写到缓冲区中
	* 直到缓冲区写满, BufferedOutputStream才会把缓冲区中的数据一次性写到文件里
* D.拷贝的代码 

		```java
	FileInputStream fis = new FileInputStream("致青春.mp3");			//创建文件输入流对象,关联致青春.mp3
	BufferedInputStream bis = new BufferedInputStream(fis);			//创建缓冲区对fis装饰
	FileOutputStream fos = new FileOutputStream("copy.mp3");		//创建输出流对象,关联copy.mp3
	BufferedOutputStream bos = new BufferedOutputStream(fos);		//创建缓冲区对fos装饰
		
	int b;
	while((b = bis.read()) != -1) {		
		bos.write(b);
	}
	
	bis.close();						//只关装饰后的对象即可
	bos.close();
	```
	
* E.小数组的读写和带Buffered的读取哪个更快?
	* 定义小数组如果是8192个字节大小和Buffered比较的话
	* 定义小数组会略胜一筹,因为读和写操作的是同一个数组
	* 而Buffered操作的是两个数组

### flush和close方法的区别

* flush()方法
	
	* 用来刷新缓冲区的,刷新后可以再次写出 
	
* close()方法
	
	* 用来关闭流释放资源的的,如果是带缓冲区的流对象的close()方法,不但会关闭流,还会再关闭流之前刷新缓冲区,关闭后不能再写出 
	
	```java
	package com.heima.stream;
	
	import java.io.BufferedInputStream;
	import java.io.BufferedOutputStream;
	import java.io.FileInputStream;
	import java.io.FileNotFoundException;
	import java.io.FileOutputStream;
	import java.io.IOException;
	
	public class Demo5_BufferCopy {
	
		/**
		 * @param args
		 * @throws IOException 
		 * close方法
		 * 具备刷新的功能,在关闭流之前,就会先刷新一次缓冲区,将缓冲区的字节全都刷新到文件上,再关闭,close方法刷完之后就能写了
		 * flush方法?
		 * 具备刷新的功能,刷完之后还可以继续写
		 */
		public static void main(String[] args) throws IOException {
			//demo1();
			//flush和close方法的区别
			BufferedInputStream bis = new BufferedInputStream(new FileInputStream("致青春.mp3"));
			BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("copy.mp3"));
			
			int b;
			while((b = bis.read()) != -1) {
				bos.write(b);
			}
			bis.close();
			bos.close();
		}
	
		public static void demo1() throws FileNotFoundException, IOException {
			FileInputStream fis = new FileInputStream("致青春.mp3");				//创建输入流对象,关联致青春.mp3
			FileOutputStream fos = new FileOutputStream("copy.mp3");			//创建输出流对象,关联copy.mp3
			BufferedInputStream bis = new BufferedInputStream(fis);				//创建缓冲区对象,对输入流进行包装让其变得更加强大
			BufferedOutputStream bos = new BufferedOutputStream(fos);
			
			int b;
			while((b = bis.read()) != -1) {
				bos.write(b);
			}
			
			bis.close();
			bos.close();
		}
	}
	```

### 字节流读写中文

* 字节流读取中文的问题
	
	* 字节流在读中文（一个字两个字节）的时候有可能会读到半个中文,造成乱码 
	
* 字节流写出中文的问题
	* 字节流直接操作的字节,所以写出中文必须将字符串转换成字节数组 
	* 写出回车换行 write("\r\n".getBytes());
	
	```java
	package com.heima.stream;
	
	import java.io.FileInputStream;
	import java.io.FileNotFoundException;
	import java.io.FileOutputStream;
	import java.io.IOException;
	
	public class Demo6_Chinese {
	
		/**
		 * @param args
		 * * 字节流读取中文的问题
				* 字节流在读中文的时候有可能会读到半个中文,造成乱码 
			* 字节流写出中文的问题
				* 字节流直接操作的字节,所以写出中文必须将字符串转换成字节数组 
				* 写出回车换行 write("\r\n".getBytes());
		 * @throws IOException 
		 */
		public static void main(String[] args) throws IOException {
			//demo1();
			FileOutputStream fos = new FileOutputStream("zzz.txt");
			fos.write("我读书少,你不要骗我".getBytes());
			fos.write("\r\n".getBytes());
			fos.close();
		}
	
		public static void demo1() throws FileNotFoundException, IOException {
			FileInputStream fis = new FileInputStream("yyy.txt");
			byte[] arr = new byte[4];
			int len;
			while((len = fis.read(arr)) != -1) {
				System.out.println(new String(arr,0,len));
			}
			
			fis.close();
		}
	}
	```

### 流的标准处理异常代码

* try{} finally{}嵌套（1.6版本及其以前）

* try() {} 自动close小括号里面的对象（1.7版本）

		* 原理：
	
			在try()中创建的流对象必须实现了AutoCloseable这个接口,如果实现了,在try后面的{}(读写代码)执行后就会自动调用,流对象的close方法将流关掉 
	
	```java
	package com.heima.stream;
	
	import java.io.FileInputStream;
	import java.io.FileNotFoundException;
	import java.io.FileOutputStream;
	import java.io.IOException;
	
	public class Demo7_TryFinally {
	
		/**
		 * @param args
		 * @throws IOException 
		 */
		public static void main(String[] args) throws IOException {
			//demo1();
			try(
				FileInputStream fis = new FileInputStream("xxx.txt");
				FileOutputStream fos = new FileOutputStream("yyy.txt");
				MyClose mc = new MyClose();  //如果不实现AutoCloseable接口的clouse()方法，则不会创建成功，因为不具备自动关闭功能
			){
				int b;
				while((b = fis.read()) != -1) {
					fos.write(b);
				}
			}
		}
	
		public static void demo1() throws FileNotFoundException, IOException {
			FileInputStream fis = null;
			FileOutputStream fos = null;
			try {
				fis = new FileInputStream("xxx.txt");
				fos = new FileOutputStream("yyy.txt");
				
				int b;
				while((b = fis.read()) != -1) {
					fos.write(b);
				}
			}finally {
				try{
					if(fis != null)
						fis.close();
				}finally {							//try fianlly的嵌套目的是能关一个尽量关一个
					if(fos != null)
						fos.close();
				}
			}
		}
	
	}
	
	class MyClose implements AutoCloseable {
		public void close() {
			System.out.println("我关了");
		}
	}
	```

### 图片加密

```java
package com.heima.test;

import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;

public class Test1 {

	/**
	 * @param args
	 * @throws IOException 
	 * 将写出的字节异或上一个数,这个数就是密钥,解密的时候再次异或就可以了
	 */
	public static void main(String[] args) throws IOException {
		BufferedInputStream bis = new BufferedInputStream(new FileInputStream("copy.jpg"));
		BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("copy2.jpg"));
		
		int b;
		while((b = bis.read()) != -1) {
			bos.write(b ^ 123);
		}
		
		bis.close();
		bos.close();
	}
}
```

### 拷贝文件

* 在控制台录入文件的路径,将文件拷贝到当前项目下

  ```java
  package com.heima.test;
  
  import java.io.BufferedInputStream;
  import java.io.BufferedOutputStream;
  import java.io.File;
  import java.io.FileInputStream;
  import java.io.FileNotFoundException;
  import java.io.FileOutputStream;
  import java.io.IOException;
  import java.util.Scanner;
  
  public class Test2 {
  
  	/**
  	 * 在控制台录入文件的路径,将文件拷贝到当前项目下
  	 * 
  	 * 分析:
  	 * 
  	 * 1,定义方法对键盘录入的路径进行判断,如果是文件就返回
  	 * 2,在主方法中接收该文件
  	 * 3,读和写该文件
  	 * @throws IOException 
  	 */
  	public static void main(String[] args) throws IOException {
  		File file = getFile();					//获取文件
  		BufferedInputStream  bis = new BufferedInputStream(new FileInputStream(file));
  		BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(file.getName()));
  		
  		int b;
  		while((b = bis.read()) != -1) {
  			bos.write(b);
  		}
  		
  		bis.close();
  		bos.close();
  	}
  
  	/*
  	 * 定义一个方法获取键盘录入的文件路径,并封装成File对象返回
  	 * 1,返回值类型File
  	 * 2,参数列表无
  	 */
  	public static File getFile() {
  		Scanner sc = new Scanner(System.in);				//创建键盘录入对象
  		System.out.println("请输入一个文件的路径:");
  		while(true) {
  			String line = sc.nextLine();					//接收键盘录入的路径
  			File file = new File(line);						//封装成File对象,并对其进行判断
  			if(!file.exists()) {
  				System.out.println("您录入的文件路径不存在,请重新录入:");
  			}else if(file.isDirectory()) {
  				System.out.println("您录入的是文件夹路径,请重新录入:");
  			}else {
  				return file;
  			}
  		}
  	}
  }
  ```

### 录入数据拷贝到文件

* 将键盘录入的数据拷贝到当前项目下的text.txt文件中,键盘录入数据当遇到quit时就退出

  ```java
  package com.heima.test;
  
  import java.io.FileNotFoundException;
  import java.io.FileOutputStream;
  import java.io.IOException;
  import java.util.Scanner;
  
  public class Test3 {
  
  	/**
  	 * 将键盘录入的数据拷贝到当前项目下的text.txt文件中,键盘录入数据当遇到quit时就退出
  	 * 
  	 * 分析:
  	 * 1,创建键盘录入对象
  	 * 2,创建输出流对象,关联text.txt文件
  	 * 3,定义无限循环
  	 * 4,遇到quit退出循环
  	 * 5,如果不quit,就将内容写出
  	 * 6,关闭流
  	 * @throws IOException 
  	 */
  	public static void main(String[] args) throws IOException {
  		//1,创建键盘录入对象
  		Scanner sc = new Scanner(System.in);
  		//2,创建输出流对象,关联text.txt文件
  		FileOutputStream fos = new FileOutputStream("text.txt");
  		System.out.println("请输入数据:");
  		//3,定义无限循环
  		while(true) {
  			String line = sc.nextLine();					//将键盘录入的数据存储在line中
  			//4,遇到quit退出循环
  			if("quit".equals(line)) {
  				break;
  			}
  			//5,如果不quit,就将内容写出
  			fos.write(line.getBytes());						//字符串写出必须转换成字节数组
  			fos.write("\r\n".getBytes());
  		}
  		//6,关闭流
  		fos.close();
  	}
  }
  ```
