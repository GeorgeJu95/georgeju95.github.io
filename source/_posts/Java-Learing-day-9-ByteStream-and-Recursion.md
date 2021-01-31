---
title: Java：字节流 & 递归
date: 2020-03-02 17:15:36
categories: 
toc: true
tags: [Java]
---

### 字符流FileReader

* 1.字符流是什么
	* 字符流是可以直接读写字符的IO流
	* 字符流读取字符, 就要先读取到字节数据, 然后转为字符. 如果要写出字符, 需要把字符转为字节再写出.    

<!-- more -->

* 2.FileReader
	
	* FileReader类的read()方法可以按照字符大小读取
	
	```java
	package com.heima.chario;
	
	import java.io.FileNotFoundException;
	import java.io.FileReader;
	import java.io.IOException;
	
	public class Demo1_FileReader {
	
		/**
		 * @param args
		 * @throws IOException 
		 */
		public static void main(String[] args) throws IOException {
			//demo1();
			FileReader fr = new FileReader("xxx.txt");
			int c;
			
			while((c = fr.read()) != -1) {					//通过项目默认的码表一次读取一个字符
				System.out.print((char)c);
			}
			
			fr.close();
		}
	
		public static void demo1() throws FileNotFoundException, IOException {
			FileReader fr = new FileReader("xxx.txt");
			int x = fr.read();
			System.out.println(x);
			char c = (char)x;
			System.out.println(c);
			fr.close();
		}
	}
	```

### 字符流FileWriter

* FileWriter类的write()方法可以自动把字符转为字节写出

		```java
	package com.heima.chario;
	
	import java.io.FileWriter;
	import java.io.IOException;
	
	public class Demo2_FileWriter {
	
		/**
		 * @param args
		 * @throws IOException 
		 */
		public static void main(String[] args) throws IOException {
			FileWriter fw = new FileWriter("yyy.txt");
			fw.write("大家好,基础班快接近尾声了,大家要努力,要坚持!!!");
			fw.write(97);
			fw.close();
		}
	}
	```

### 字符流的拷贝

```java
package com.heima.chario;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

public class Demo3_Copy {

	/**
	 * @param args
	 * @throws IOException 
	 */
	public static void main(String[] args) throws IOException {
		//demo1();
		//demo2();	
		//demo3();  自定义字符数组的拷贝
    
		BufferedReader br = new BufferedReader(new FileReader("xxx.txt"));
		BufferedWriter bw = new BufferedWriter(new FileWriter("yyy.txt"));
		
		int c;
		while((c = br.read()) != -1) {
			bw.write(c);
		}
		
		br.close();
		bw.close();
	}

	public static void demo3() throws FileNotFoundException, IOException {
		FileReader fr = new FileReader("xxx.txt");
		FileWriter fw = new FileWriter("yyy.txt");
		
		char[] arr = new char[1024];
		int len;
		while((len = fr.read(arr)) != -1) {			//将文件上的数据读取到字符数组中
			fw.write(arr,0,len);					//将字符数组中的数据写到文件上
		}
		
		fr.close();
		fw.close();
	}

	public static void demo2() throws FileNotFoundException, IOException {
		//字符流不能拷贝纯文本的文件
		FileReader fr = new FileReader("双元.jpg");
		FileWriter fw = new FileWriter("copy.jpg");
		
		int c;
		while((c = fr.read()) != -1) {
			fw.write(c);
		}
		
		fr.close();
		fw.close();
	}

	public static void demo1() throws FileNotFoundException, IOException {
		FileReader fr = new FileReader("xxx.txt");
		FileWriter fw = new FileWriter("zzz.txt");
		
		int c;
		while((c = fr.read()) != -1) {
			fw.write(c);
		}
		
		fr.close();
		fw.close();				//Writer类中有一个2k的小缓冲区,如果不关流,就会将内容写到缓冲区里,关流会将缓冲区内容刷新,再关闭
	}
}
```

### 什么情况下使用字符流

* 字符流也可以拷贝文本文件, 但**不推荐使用**. 因为读取时会把字节转为字符, 写出时还要把字符转回字节.
* 程序需要**读取一段文本**, 或者需要**写出一段文本**的时候可以使用字符流
* **读取的时候是按照字符的大小读取的,不会出现半个中文**
* **写出的时候可以直接将字符串写出,不用转换为字节数组**

### 字符流是否可以拷贝非纯文本的文件

* **不可以拷贝非纯文本的文件**
* 因为在读的时候会将字节转换为字符,在转换过程中,可能找不到对应的字符,就会用?代替,写出的时候会将字符转换成字节写出去
* 如果是?,直接写出,这样写出之后的文件就乱了,看不了了 	

### readLine()和newLine()方法

* BufferedReader的readLine()方法可以读取一行字符(**不包含换行符号**)

* BufferedWriter的newLine()可以**输出一个跨平台的换行符号"\r\n"**

  ```java
  package com.heima.chario;
  
  import java.io.BufferedReader;
  import java.io.BufferedWriter;
  import java.io.FileNotFoundException;
  import java.io.FileReader;
  import java.io.FileWriter;
  import java.io.IOException;
  
  public class Demo4_Buffered {
  
  	/**
  	 * @param args
  	 * 带缓冲区的流中的特殊方法
  	 * readLine()
  	 * newLine();
  	 * 
  	 * newLine()与\r\n的区别
  	 * newLine()是跨平台的方法
  	 * \r\n只支持的是windows系统
  	 * @throws IOException 
  	 */
  	public static void main(String[] args) throws IOException {
  		//demo1();
  		BufferedReader br = new BufferedReader(new FileReader("zzz.txt"));
  		BufferedWriter bw = new BufferedWriter(new FileWriter("aaa.txt"));
  		
  		String line;
  		while((line = br.readLine()) != null) {
  			bw.write(line);
  			//bw.newLine();							//写出回车换行符
  			//bw.write("\r\n");         //不跨平台，仅限Windows
  		}
  		
  		br.close();
  		bw.close();
  	}
  
  	public static void demo1() throws FileNotFoundException, IOException {
  		BufferedReader br = new BufferedReader(new FileReader("zzz.txt"));
  		String line;
  		
  		while((line = br.readLine()) != null) {
  			System.out.println(line);
  		}
  		
  		br.close();
  	}
  }
  ```

### 将文本反转

* 将一个文本文档上的文本反转,第一行和倒数第一行交换,第二行和倒数第二行交换

  ```java
  package com.heima.test;
  
  import java.io.BufferedReader;
  import java.io.BufferedWriter;
  import java.io.FileNotFoundException;
  import java.io.FileReader;
  import java.io.FileWriter;
  import java.io.IOException;
  import java.util.ArrayList;
  
  public class Test1 {
  
  	/**
  	 * @param args
  	 * 将一个文本文档上的文本反转,第一行和倒数第一行交换,第二行和倒数第二行交换
  	 * 
  	 * 分析:
  	 * 1,创建输入输出流对象
  	 * 2,创建集合对象
  	 * 3,将读到的数据存储在集合中
  	 * 4,倒着遍历集合将数据写到文件上
  	 * 5,关流
  	 * @throws IOException 
  	 * 
  	 * 注意事项:
  	 * 流对象尽量晚开早关
  	 */
  	public static void main(String[] args) throws IOException {
  		//改写后是尽量晚开早关
  		// 1,创建输入输出流对象
  		BufferedReader br = new BufferedReader(new FileReader("zzz.txt"));
  		
  		//2,创建集合对象
  		ArrayList<String> list = new ArrayList<>();
  		//3,将读到的数据存储在集合中
  		String line;
  		while((line = br.readLine()) != null) {
  			list.add(line);
  		}
  		br.close();											//关流
  		
  		//4,倒着遍历集合将数据写到文件上
  		BufferedWriter bw = new BufferedWriter(new FileWriter("revzzz.txt"));
  		for(int i = list.size() - 1; i >= 0; i--) {
  			bw.write(list.get(i));
  			bw.newLine();
  		}
  		//5,关流
  		
  		bw.close();
  	}
  }
  ```

### LineNumberReader

* LineNumberReader是BufferedReader的子类, 具有相同的功能, 并且可以统计行号
	* 调用getLineNumber()方法可以获取当前行号
	* 调用setLineNumber()方法可以设置当前行号
	
	```java
	package com.heima.chario;
	
	import java.io.FileNotFoundException;
	import java.io.FileReader;
	import java.io.IOException;
	import java.io.LineNumberReader;
	
	public class Demo5_LineNumberReader {
	
		/**
		 * @param args
		 * @throws IOException 
		 */
		public static void main(String[] args) throws IOException {
			LineNumberReader lnr = new LineNumberReader(new FileReader("zzz.txt"));
			
			String line;
			lnr.setLineNumber(100);
			while((line = lnr.readLine()) != null) {
				System.out.println(lnr.getLineNumber() + ":" + line);
			}
			
			lnr.close();
		}
	}
	```

### 装饰设计模式

```java
package com.heima.chario;

public class Demo6_Wrap {

	/**
	 * @param args
	 * 装饰设计模式的好处是:
	 * 耦合性不强,被装饰的类的变化与装饰类的变化无关
	 */
	public static void main(String[] args) {
		HeiMaStudent hms = new HeiMaStudent(new Student());
		hms.code();
	}

}

interface Coder {
	public void code();
}

class Student implements Coder {

	@Override
	public void code() {
		System.out.println("javase");
		System.out.println("javaweb");
	}
	
}

class HeiMaStudent implements Coder {
	//1,获取被装饰类的引用
	private Student s;						//获取学生引用
	
	//2,在构造方法中传入被装饰类的对象
	public HeiMaStudent(Student s) {
		this.s = s;
	}

	//3,对原有的功能进行升级
	@Override
	public void code() {
		s.code();
		System.out.println("ssh");
		System.out.println("数据库");
		System.out.println("大数据");
		System.out.println("...");
	}
}
```

### 使用指定的码表读写字符

* FileReader是使用默认码表读取文件, 如果需要使用指定码表读取, 那么可以使用InputStreamReader(字节流, 编码表)，它是字节流通向字符流的桥梁

* FileWriter是使用默认码表写出文件, 如果需要使用指定码表写出, 那么可以使用OutputStreamWriter(字节流, 编码表)，它是字符流通向字节流的桥梁 

  ```java
  package com.heima.chario;
  
  import java.io.BufferedReader;
  import java.io.BufferedWriter;
  import java.io.FileInputStream;
  import java.io.FileNotFoundException;
  import java.io.FileOutputStream;
  import java.io.FileReader;
  import java.io.FileWriter;
  import java.io.IOException;
  import java.io.InputStreamReader;
  import java.io.OutputStreamWriter;
  import java.io.UnsupportedEncodingException;
  
  public class Demo7_TransIO {
  
  	/**
  	 * @param args
  	 * @throws IOException 
  	 */
  	public static void main(String[] args) throws IOException {
  		//demo1();
  		//demo2();
  		BufferedReader br = 								//更高效的读
  				new BufferedReader(new InputStreamReader(new FileInputStream("utf-8.txt"), "utf-8"));
  		BufferedWriter bw = 								//更高效的写
  				new BufferedWriter(new OutputStreamWriter(new FileOutputStream("gbk.txt"), "gbk"));
  		int c;
  		while((c = br.read()) != -1) {
  			bw.write(c);
  		}
  		
  		br.close();
  		bw.close();
  	}
  
  	public static void demo2() throws UnsupportedEncodingException,
  			FileNotFoundException, IOException {
  		InputStreamReader isr = new InputStreamReader(new FileInputStream("utf-8.txt"), "uTf-8");	//指定码表读字符
  		OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("gbk.txt"), "gbk");	//指定码表写字符
  		
  		int c;
  		while((c = isr.read()) != -1) {
  			osw.write(c);
  		}
  		
  		isr.close();
  		osw.close();
  	}
  
  	public static void demo1() throws FileNotFoundException, IOException {
  		//用默认编码表读写,出现乱码
  		FileReader fr = new FileReader("utf-8.txt");				
  		FileWriter fw = new FileWriter("gbk.txt");
  		
  		int c;
  		while((c = fr.read()) != -1) {
  			fw.write(c);
  		}
  		
  		fr.close();
  		fw.close();
  	}
  }
  ```

### 转换流图解

![转换流](/assets/blogImg/转换流.png)

### 获取文本上字符出现的次数

* 获取一个文本上每个字符出现的次数,将结果写在times.txt上

  ```java
  package com.heima.test;
  
  import java.io.BufferedReader;
  import java.io.BufferedWriter;
  import java.io.FileNotFoundException;
  import java.io.FileReader;
  import java.io.FileWriter;
  import java.io.IOException;
  import java.util.HashMap;
  
  public class Test2 {
  
  	/**
  	 * @param args
  	 * 获取一个文本上每个字符出现的次数,将结果写在times.txt上
  	 * 
  	 * 1,创建带缓冲区的输入流对象
  	 * 2,创建双列集合对象,目的是把字符当作键,把字符出现的次数当作值
  	 * 3,通过读取不断向集合中存储,存储的时候要判断,如果不包含这个键就将键和值为1存储,如果包含就将键和值加1存储
  	 * 4,关闭输入流
  	 * 5,创建输出流对象
  	 * 6,将结果写出
  	 * 7,关闭输出流
  	 * @throws IOException 
  	 */
  	public static void main(String[] args) throws IOException {
  		//1,创建带缓冲区的输入流对象
  		BufferedReader br = new BufferedReader(new FileReader("zzz.txt"));
  		//2,创建双列集合对象,目的是把字符当作键,把字符出现的次数当作值
  		HashMap<Character, Integer> hm = new HashMap<>();
  		//3,通过读取不断向集合中存储,存储的时候要判断,如果不包含这个键就将键和值为1存储,如果包含就将键和值加1存储
  		int c;
  		while((c = br.read()) != -1) {
  			char ch = (char)c;
  			/*if(!hm.containsKey(ch)) {
  				hm.put(ch, 1);
  			}else {
  				hm.put(ch, hm.get(ch) +  1);
  			}*/
  			
  			hm.put(ch, !hm.containsKey(ch)? 1 : hm.get(ch) + 1);
  		}
  		//4,关闭输入流
  		br.close();
  		//5,创建输出流对象
  		BufferedWriter bw = new BufferedWriter(new FileWriter("times.txt"));
  		//6,将结果写出
  		
  		for (Character key : hm.keySet()) {
  			bw.write(key + "=" + hm.get(key));
  		}
  		
  		bw.close();
  	}
  }
  ```

### 试用版软件

* 当我们下载一个试用版软件,没有购买正版的时候,每执行一次就会提醒我们还有多少次使用机会用学过的IO流知识,模拟试用版软件,试用10次机会,执行一次就提示一次您还有几次机会,如果次数到了提示请购买正版

  ```java
  package com.heima.test;
  
  import java.io.BufferedReader;
  import java.io.FileReader;
  import java.io.FileWriter;
  import java.io.IOException;
  
  public class Test4 {
  
  	/**
  	 *  当我们下载一个试用版软件,没有购买正版的时候,每执行一次就会提醒我们还有多少次使用机会用学过的IO流知识,模拟试用版软件,
  	 *  试用10次机会,执行一次就提示一次您还有几次机会,如果次数到了提示请购买正版
  	 * @throws IOException 
  	 * 分析:
  	 * 1,创建带缓冲的输入流对象,因为要使用readLine方法,可以保证数据的原样性
  	 * 2,将读到的字符串转换为int数
  	 * 3,对int数进行判断,如果大于0,就将其--写回去,如果不大于0,就提示请购买正版
  	 * 4,在if判断中要将--的结果打印,并将结果通过输出流写到文件上
  	 */
  	public static void main(String[] args) throws IOException {
  		//1,创建带缓冲的输入流对象,因为要使用readLine方法,可以保证数据的原样性
  		BufferedReader br = new BufferedReader(new FileReader("config.txt"));
  		//2,将读到的字符串转换为int数
  		String line = br.readLine();
  		int times = Integer.parseInt(line);					//将数字字符串转换为数字
  		//3,对int数进行判断,如果大于0,就将其--写回去,如果不大于0,就提示请购买正版
  		if(times > 0) {
  			//4,在if判断中要将--的结果打印,并将结果通过输出流写到文件上
  			System.out.println("您还有" + times-- + "次机会");
  			FileWriter fw = new FileWriter("config.txt");
  			fw.write(times + "");
  			fw.close();
  		}else {
  			System.out.println("您的试用次数已到,请购买正版");
  		}
  		//关闭流
  		br.close();
  	}
  }
  ```

### 练习（文件+递归）

* 需求:从键盘输入接收一个文件夹路径,打印出该文件夹下所有的.java文件名

  ```java
  package com.heima.test;
  
  import java.io.File;
  import java.io.FileReader;
  import java.util.Scanner;
  
  public class Test5 {
  
  	/**
  	 * 需求:从键盘输入接收一个文件夹路径,打印出该文件夹下所有的.java文件名
  	 * 
  	 * 分析:
  	 * 从键盘接收一个文件夹路径
  	 * 1,如果录入的是不存在,给与提示
  	 * 2,如果录入的是文件路径,给与提示
  	 * 3,如果是文件夹路径,直接返回
  	 * 
  	 * 打印出该文件夹下所有的.java文件名
  	 * 1,获取到该文件夹路径下的所有的文件和文件夹,存储在File数组中
  	 * 2,遍历数组,对每一个文件或文件夹做判断
  	 * 3,如果是文件,并且后缀是.java的,就打印
  	 * 4,如果是文件夹,就递归调用
  	 */
  	public static void main(String[] args) {
  		File dir = getDir();
  		printJavaFile(dir);
  	}
  
  	/*
  	 * 获取键盘录入的文件夹路径
  	 * 1,返回值类型File
  	 * 2,不需要有参数
  	 */
  	public static File getDir() {
  		Scanner sc = new Scanner(System.in);				//创建键盘录入对象
  		System.out.println("请输入一个文件夹路径");
  		while(true) {
  			String line = sc.nextLine();					//将键盘录入的文件夹路径存储
  			File dir = new File(line);						//封装成File对象
  			if(!dir.exists()) {
  				System.out.println("您录入的文件夹路径不存在,请重新录入");
  			}else if(dir.isFile()) {
  				System.out.println("您录入的是文件路径,请重新录入文件夹路径");
  			}else {
  				return dir;
  			}
  		}
  	}
  	/*
  	 * 获取文件夹路径下的所.java文件
  	 * 1,返回值类型 void
  	 * 2,参数列表File dir
  	 */
  	public static void printJavaFile(File dir) {
  		//1,获取到该文件夹路径下的所有的文件和文件夹,存储在File数组中
  		File[] subFiles = dir.listFiles();
  		//2,遍历数组,对每一个文件或文件夹做判断
  		for (File subFile : subFiles) {
  			//3,如果是文件,并且后缀是.java的,就打印
  			if(subFile.isFile() && subFile.getName().endsWith(".java")) {
  				System.out.println(subFile);
  			//4,如果是文件夹,就递归调用
  			}else if (subFile.isDirectory()){
  				printJavaFile(subFile);
  			}
  		}
  	}
  }
  ```

  ```java
  package com.heima.test;
  
  import java.io.BufferedInputStream;
  import java.io.BufferedOutputStream;
  import java.io.File;
  import java.io.FileInputStream;
  import java.io.FileNotFoundException;
  import java.io.FileOutputStream;
  import java.io.IOException;
  
  public class Test3 {
  
  	/**
  	 * 需求:从键盘接收两个文件夹路径,把其中一个文件夹中(包含内容)拷贝到另一个文件夹中
  	 * 
  	 * 把其中一个文件夹中(包含内容)拷贝到另一个文件夹中
  	 * 分析:
  	 * 1,在目标文件夹中创建原文件夹
  	 * 2,获取原文件夹中所有的文件和文件夹,存储在File数组中
  	 * 3,遍历数组
  	 * 4,如果是文件就用io流读写
  	 * 5,如果是文件夹就递归调用
  	 * @throws IOException 
  	 */
  	public static void main(String[] args) throws IOException {
  		File src = Test1.getDir();
  		File dest = Test1.getDir();
  		if(src.equals(dest)) {
  			System.out.println("目标文件夹是源文件夹的子文件夹");
  		}else {
  			copy(src,dest);
  		}
  	}
  	/*
  	 * 把其中一个文件夹中(包含内容)拷贝到另一个文件夹中
  	 * 1,返回值类型void
  	 * 2,参数列表File src,File dest
  	 */
  	public static void copy(File src, File dest) throws IOException {
  		//1,在目标文件夹中创建原文件夹
  		File newDir = new File(dest, src.getName());
  		newDir.mkdir();
  		//2,获取原文件夹中所有的文件和文件夹,存储在File数组中
  		File[] subFiles = src.listFiles();
  		//3,遍历数组
  		for (File subFile : subFiles) {
  			//4,如果是文件就用io流读写
  			if(subFile.isFile()) {
  				BufferedInputStream bis = new BufferedInputStream(new FileInputStream(subFile));
  				BufferedOutputStream bos = 
  						new BufferedOutputStream(new FileOutputStream(new File(newDir,subFile.getName())));
  				
  				int b;
  				while((b = bis.read()) != -1) {
  					bos.write(b);
  				}
  				
  				bis.close();
  				bos.close();
  			//5,如果是文件夹就递归调用
  			}else {
  				copy(subFile,newDir);
  			}
  		}
  	}
  }
  ```

### 总结

* 1.会用BufferedReader读取GBK码表和UTF-8码表的字符
* 2.会用BufferedWriter写出字符到GBK码表和UTF-8码表的文件中
* 3.会使用BufferedReader从键盘读取一行