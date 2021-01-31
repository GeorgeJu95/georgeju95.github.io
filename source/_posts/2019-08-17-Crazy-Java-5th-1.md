---
title: 疯狂Java讲义（5th）阅读总结（一）：Unit1 - Unit6
date: 2019-08-17 16:17:36
categories: 
toc: true
tags: [Java]
---

### Unit 1 Java语言概述与开发环境

* 高级语言的运行机制：
  * 编译型语言：一次性编译成机器码，可以脱离开发环境独立运行，而且通常运行效率较高。如C、C++、Objective-C、Swift、Kotlin等等。
  * 解释型语言：使用专门的解释器对源程序逐行解释成特定平台的的机器码并立即执行的语言。如JavaScript、Ruby、Python等等。
  * 半编译型语言（伪编译型）：如Visual Basic
  * Java既不是纯粹的编译型语言，也不是纯粹的解释型语言。编译并不会生成特定平台的机器码，而是生成一种与平台无关的**字节码**。通过JVM(Java虚拟机)来解释字节码，它是Java程序跨平台的关键部分。JRE(Java运行环境)包括JVM，以及运行Java程序的其他环境支持。

<!-- more -->
* javac -d destdir srcFile。编译语句，-d destdir指定生成的字节码文件的存放路径，不加就放在当前路径下。
* 一个Java源文件最多只能定义一个public类，**源程序的主文件名必须和该public类的类名相同**。如果没有pubic类，源文件的主文件名可以任意。
* 一个Java源文件通常只定义一个类，不同的类使用不同的源文件定义。
* 交互式工具**jshell**，它是一个REPL(Read-Eval-Print Loop)工具，该工具是一个交互式的命令行界面，可用于执行Java语言的变量声明、语句和表达式，而且可以立即看到结果。因此可以用该工具快速学习Java或测试Java的新API。命令行输入jshell进入交互模式，/help来查看帮助信息。
* **垃圾收回器**，传统C/C++语言需要显示进行垃圾回收，对程序员来说是比较麻烦的事情。如果一些分配出去的内存得不到及时回收，就会引起系统运行速度的下降，甚至导致程序瘫痪，这种现象称为**内存泄露**。Java程序的内存分配和回收都是由JRE在后台自动进行的，垃圾回收(GC)是一种**动态存储管理技术**，它自动释放不再被程序引用的对象，按照特定的垃圾回收算法来实现内存资源的自动回收功能。
  * 垃圾回收器的工作目标是回收无用对象的内存空间，这些内存空间都是JVM堆内存里的内存空间，垃圾回收器只能回收内存资源，对其他物理资源，如数据库连接、磁盘I/O等资源则无能为力。
  * 将对象的引用变量置为null，暗示可以回收该对象
  * 不同JVM采用了不同的垃圾回收机制和不同的垃圾回收算法，垃圾回收发生的**不可预知性**。
  * CMS ---> G1（Java7） ---> ZGC（Java11）; 永生代内存(PermGen) ---> 元空间(Metaspace)。P20

### Unit 2 理解面向对象

* 面向对象实际上由：OOA(分析)、OOD(设计)和OOP(编程)组成，其中OOA和OOD的结构需要使用一种方式来描述并记录，目前业界统一采用UML(统一建模语言)。
* 成员变量(状态数据) + 方法(行为) = 类定义。面向对象比面向过程的编程粒度要大，因此面向对象比面向过程更简单、易用。
* 面向对象的三个基本特征：**封装、继承和多态**。除此之外，抽象也是面向对象的重要部分。
* 注意区分**基于对象**的概念。基于对象无法利用现有的对象模板产生新的对象类型，继而产生新的对象，也就是它没有继承的特定，而多态更需要继承，因此也没有多态的特点。例如JavaScript就是基于对象的语言，开发者只能使用现有对象的方法和属性。
* **UML**是一种用于描述、记录软件分析设计的工具，最常用的UML图包括：用例图、类图、组件图、部署图、顺序图、活动图和状态机图。
* Java语言中，除了8个基本数据类型值之外，一切都是对象。对象是Java程序的核心，所以Java里的对象具有唯一性，每个对象都有一个标识来引用它，如果失去了标识，这个对象将变成垃圾。Java语言不允许直接访问对象，而是通过对对象的引用来操作对象。
* class关键字定义类，定义类时可使用成员变量来描述该类对象的数据，可使用方法来描述该类对象的行为特征。若干类直接有一定的结构关系：
  * 一般 → 特殊的关系：典型的继承关系，extends关键字来表示这种**继承关系**。
  * 整体 → 部分的关系：通过在一个类里保存另一个对象的引用来实现这种**组合关系**。

### Unit 3 数据类型和运算符

* 文档注释javadoc，以（/\*\*）开始，以（\*/）结束。再通过javadoc命令可以对源文件、包生成API文档。

* 标识符只能包含美元符$，不能包含@、#等特殊字符。

* 51个关键字，两个保留字goto、const，三个特殊的直接量 (literal) true、false、null。

* Java语言支持的类型：**基本类型**：boolean类型和数值类型（注意byte占一个字节，char占两个，因为Java采用16位的Unicode字符集），**引用类型**（包括类，接口和数组，还有null）

* 整数值的4种表示方式，十进制，二进制（0b或0B开头），八进制（0开头）和十六进制（0x或0X）

* Java的浮点类型默认是double类型，所以在末尾加上f或F表示float。一个整数如果很小并且前面有byte或short，则它是对应的byte或short类型；如果数字很大，并且前面是long，但是Java不会把该数字当成long型，所以要在数字后面加上字母l或L，不然就把数字默认为int造成溢出错误。单单一个整数是默认为整型的。

* 三个特殊的浮点数值，POSITIVE_INFINITY（正浮点数/0），NEGATIVE_INFINITY（负浮点数/0）和NaN（0.0/0.0或对一个负数开方）。所有的正无穷相等，所有的负无穷相等，但是NaN不与任何一个数值相等，包括自己。整数除以0会抛出一个异常。

* 数值中可以使用下划线分隔，包括整数和浮点数，便于分清楚有多少位。

* boolean类型的数值只能是true或false，不能用0或非0来表示。其他基本类型的值也不能转成boolean类型。

* Java10开始支持使用**var**定义局部变量；var相当于一个动态类型，类型由编译器自动推断，所以必须在定义局部变量的同时指定初始值。使用var定义的变量也可以是字符串等引用类型。

* 自动类型转换

![image-1](/assets/blogImg/CrazyJava1.png)

* （+）号不仅可以作为加法运算符，还可以作为字符串连接运算符。（-）号除了可以作为减法运算符外，还可以作为求负数的运算符。
* Java为8种基本类型都提供了对应的**包装类**：boolean对应Boolean、byte对应Byte、short对应Short、int对应Integer、long对应Long、char对应Character、float对应Float、double对应Double，8个包装类都提供了一个**parseXxx(String str)**静态方法用于**将字符串转换成基本类型**
* 关于字符串直接量有一点需要指出，当程序第一次使用某个字符串直接量时，Java会使用**常量池**来缓存该字符串直接量，如果程序后面的部分需要用到该字符串直接量时，Java会直接使用常量池中的字符串直接量。Java会确保每个字符串常量只有一个，不会产生多个副本。
* **常量池**是在编译期间被确定，并被保存在已编译的.class文件中的一些数据。它包括关于类、方法、接口中的常量，也包括字符串直接量。
* 取余运算

```java
public class ModTest
{
	public static void main(String[] args)
	{
		var a = 5.2;
		var b = 3.1;
		var mod = a % b;

		System.out.println(mod); // mod的值为2.1
		System.out.println("5对0.0求余的结果是:" + 5 % 0.0); // 输出非数:NaN
		System.out.println("-5.0对0求余的结果是:" + -5.0 % 0); // 输出非数:NaN
		System.out.println("0对5.0求余的结果是:" + 0 % 5.0); // 输出0.0
		System.out.println("0对0.0求余的结果是:" + 0 % 0.0); // 输出非数:NaN
		// 下面代码将出现异常：java.lang.ArithmeticException: / by zero
		System.out.println("-5对0求余的结果是:" + -5 % 0);
	}
}
```

* 位运算符，<<：左移运算符，>>：右移运算符，>>>：无符号右移运算符。
  * 对于低于int类型的操作数总是先自动转换为int类型后再移位
  * 对于int类型的整数移位，a>>b，如果b>32，系统先用b对32求余，得到的结果才是真正移位的位数。a>>32的结果和a相同。

* 逻辑运算符：&&：与，&：不短路与。||：或，|：不短路或。
  
* 扩展后的赋值运算符，如果可以使用这种扩展后的运算符，则推荐使用它们。简洁不易出错。

```java
public class EnhanceAssignTest
{
	public static void main(String[] args)
	{
		// 定义一个byte类型的变量
		byte a = 5;
		// 下面语句出错，因为5默认是int类型，a + 5就是int类型。
		// 把int类型赋给byte类型的变量，所以出错
		a = a + 5;
		// 定义一个byte类型的变量
		byte b = 5;
		// 下面语句不会出现错误
		b += 5;
	}
}
```

### Unit 4 流程控制与数组

* 与while循环不同的是，do while循环的循环条件后必须有一个分号。
* break语句不仅可以结束其所在的循环，还可以**直接结束其外层循环**。此时需要在break后紧跟一个标签，这个标签用于标识一个外层循环。continue同理。

```java
public class BreakLabel
{
	public static void main(String[] args)
	{
		// 外层循环，outer作为标识符
		outer:
		for (var i = 0; i < 5; i++)
		{
			// 内层循环
			for (var j = 0; j < 3; j++)
			{
				System.out.println("i的值为:" + i + "  j的值为:" + j);
				if (j == 1)
				{
					// 跳出outer标签所标识的循环。
					break outer;
				}
			}
		}
	}
}
```

* 定义数组以及数组初始化：定义数组只是定义了一个引用变量，并未指向任何有效的内存空间，所以还没有内存空间来存储数组元素，因此这个数组也不能使用，只有对数组进行初始化后才可以使用。
  * **静态初始化**：初始化时由程序员显示指定每个数组元素的初始值，由系统决定数组长度。静态初始化时，显示指定的数组元素值的类型必须与new关键字后的**type类型相同**，或者是**其子类的实例**。
  * **动态初始化**：初始化时程序员只指定数组长度，由系统为数组元素分配初始值。new后type的特性与静态初始化类似。
  * 不要同时使用静态初始化和动态初始化。

```java
public class ArrayTest
{
	public static void main(String[] args)
	{
		// 定义一个int数组类型的变量，变量名为intArr.
		int[] intArr;
		// 使用静态初始化，初始化数组时只指定数组元素的初始值，不指定数组长度。
		intArr = new int[] {5, 6, 8, 20};
		// 定义一个Object数组类型的变量，变量名为objArr.
		Object[] objArr;
		// 使用静态初始化，初始化数组时数组元素的类型是
		// 定义数组时所指定的数组元素类型的子类
		objArr = new String[] {"Java", "李刚"};
		Object[] objArr2;
		// 使用静态初始化
		objArr2 = new Object[] {"Java", "李刚"};


		// 数组的定义和初始化同时完成，使用简化的静态初始化写法
		int[] a = {5, 6, 7, 9};


		//数组的定义和初始化同时完成，使用动态初始化语法
		int[] prices = new int[5];
		// 数组的定义和初始化同时完成，初始化数组时元素的类型是定义数组时元素类型的子类
		Object[] books = new String[4];


		// 编译器推断names变量的类型是String[]，静态初始化
		var names = new String[] {"yeeku", "nono"};  //不能省略new String[]
		// 编译器推断weightArr变量的类型是double[]，动态初始化
		var weightArr = new double[4];


		// 输出objArr数组的第二个元素，将输出字符串"李刚"
		System.out.println(objArr[1]);
		// 为objArr2的第一个数组元素赋值
		objArr2[0] = "Spring";

		// 访问数组元素指定的索引等于数组长度，所以下面代码将在运行时出现异常
		// System.out.println(objArr2[2]);


		// 使用循环输出prices数组的每个数组元素的值，输出都是0
		for (var i = 0; i < prices.length; i++)
		{
			System.out.println(prices[i]);
		}

		// 对动态初始化后的数组元素进行赋值
		books[0] = "疯狂Java讲义";
		books[1] = "轻量级Java EE企业应用实战";
		// 使用循环输出books数组的每个数组元素的值，最后两个输出null
		for (var i = 0; i < books.length; i++)
		{
			System.out.println(books[i]);
		}
	}
}
```

* 实际的数组对象被存储在堆（heap）内存中；如果引用该数组对象的数组引用变量是一个局部变量，那么它被存储在栈（stack）内存中。所有在方法中定义的局部变量都是放在栈内存的；创建一个对象，对象将被保存到运行时数据区，即堆内存。堆内存中的对象不会因为方法结束而销毁，因为可能还有另一个引用变量指向它。只有当一个对象没有任何引用变量引用它时，系统的垃圾回收器才会在合适的时候回收它。
* 二维数组（实质上还是一维数组，只是数组元素也是引用类型，指向一维数组）
```java
public class TwoDimensionTest
{
	public static void main(String[] args)
	{
		// 定义一个二维数组
		int[][] a;
		// 把a当成一维数组进行初始化，初始化a是一个长度为4的数组
		// a数组的数组元素又是引用类型
		a = new int[4][];
		// 把a数组当成一维数组，遍历a数组的每个数组元素
		for (int i = 0, len = a.length; i < len; i++)
		{
			System.out.println(a[i]); // null null null null
		}
		// 初始化a数组的第一个元素
		a[0] = new int[2];
		// 访问a数组的第一个元素所指数组的第二个元素
		a[0][1] = 6;
		// a数组的第一个元素是一个一维数组，遍历这个一维数组
		for (int i = 0, len = a[0].length; i < len; i++)
		{
			System.out.println(a[0][i]); // 0  6
		}

		// 同时初始化二维数组的2个维数，动态初始化
		int[][] b = new int[3][4];

		// 使用静态初始化的语法来初始化一个二维数组
		String[][] str1 = new String[][] {new String[3],
			new String[] {"hello"}};
        for(var test : str1[0]) 
			System.out.println(test);   // null null null
		// 使用简化的静态初始化语法来初始化二维数组
		String[][] str2 = {new String[3],
			new String[] {"hello"}};
		System.out.println(str1[1][0]); // hello
		System.out.println(str2[1][0]); // hello
	}
}
```

* 如果想在Java语言中实现无限扩展的数组，则可以定义一个Object[]类型的数组，这个数组的元素是Object类型，因此可以再一次指向Object[]类型.....。
* 操作数组的工具类：**Arrays**（处于java.util包下），包含的一些static修饰的方法可以直接操作数组（static修饰的方法可以直接通过类名调用）。具体可以参考API文档。Java 8增强了Arrays类的功能，增加了一些工具方法，这些工具方法可以充分利用多CPU并行的能力来提高设值、排序的性能（p105）。
```java
public class ArraysTest
{
	public static void main(String[] args)
	{
		// 定义一个a数组
		var a = new int[] {3, 4, 5, 6};
		// 定义一个a2数组
		var a2 = new int[] {3, 4, 5, 6};
		// a数组和a2数组的长度相等，每个元素依次相等，将输出true
		System.out.println("a数组和a2数组是否相等："
			+ Arrays.equals(a, a2));
		// 通过复制a数组，生成一个新的b数组
		var b = Arrays.copyOf(a, 6);
		System.out.println("a数组和b数组是否相等："
			+ Arrays.equals(a, b)); // false
		// 输出b数组的元素，将输出[3, 4, 5, 6, 0, 0]
		System.out.println("b数组的元素为："
			+ Arrays.toString(b));
		// 将b数组的第3个元素（包括）到第5个元素（不包括）赋为1
		Arrays.fill(b, 2, 4, 1);
		// 输出b数组的元素，将输出[3, 4, 1, 1, 0, 0]
		System.out.println("b数组的元素为："
			+ Arrays.toString(b));
		// 对b数组进行排序
		Arrays.sort(b);
		// 输出b数组的元素，将输出[0, 0, 1, 1, 3, 4]
		System.out.println("b数组的元素为："
			+ Arrays.toString(b));
	}
}
```

* 除此之外，在System类里也包含了一个static void **arraycopy**(Object src, int srcPos, Object dest, int destPos, int length)，该方法可以将src数组里的元素值赋给dest数组的元素，其中srcPos指定从src数组的第几个元素开始赋值，length指定复制多少个元素。

### Unit 5 面向对象（上 ）

* 定义类的修饰符有public、final、abstract，或者完全省略修饰符。对于一个类定义而言，可以包含三种最常见的成员：**构造器、成员变量和方法**。构造器用于构造该类的实例，通过new关键字来调用构造器。如果没有显式给出构造器，则系统默认生成一个无参构造器。**构造器名必须和类名相同。构造器既不能定义返回值类型，也不能使用void声明构造器没有返回值**（否则会被当成方法）。
* 定义成员变量的修饰符（可以省略）有：public、protected、private三个最多只能出现其中一个，可以与static、final组合起来修饰成员变量。定义成员变量还可以指定一个可选的默认值。
* 定义方法的修饰符（可以省略）：public、protected、private三个最多只能出现其中一个；abstract和final最多只能出现其中之一，它们可以和static组合起来修饰方法。
* 通常把**static**修饰的成员变量和方法称为**类变量、类方法**，不使用static修饰的成员变量和方法也成为**实例变量、实例方法**。**静态成员不能直接访问非静态成员。**staic真正的作用就是区分成员变量、方法、内部类、初始化块这四种成员到底属于类本身还是属于实例。有static修饰的成员属于类本身，没有static修饰的成员属于该类的实例。
* static修饰的方法和成员变量，既可以通过类来调用，也可以通过实例来调用（一般通过类来调用，不易造成混淆）。没有使用static修饰的成员只能通过实例来调用。
* **this**关键字总是指向调用该方法的对象。根据this出现位置的不同，this作为对象的默认引用有两种情形：
  * 构造器中引用该构造器正在初始化的对象
  * 在方法中引用调用该方法的对象
* this最大的作用就是**让类中的一个方法，访问该类里的另一个方法或实例变量**。this可以代表任何对象，当它出现在某个方法体中时，它所代表的对象是不确定的，但它的**类型是确定的——当前类的实例**：只有当这个方法被调用时，它所代表的对象才被确定下来。谁在调用这个方法，this就代表谁。
```java
public class Dog
{
	// 定义一个jump()方法
	public void jump()
	{
		System.out.println("正在执行jump方法");
	}
	// 定义一个run()方法，run()方法需要借助jump()方法
	public void run()
	{
		// var d = new Dog();
		// d.jump();
		// 不需要上面那么麻烦。使用this引用调用run()方法的对象
		this.jump();
		System.out.println("正在执行run方法");
	}
}
```

* 由于同一个对象两个方法之间的依赖很常见，因此Java运行对象的一个成员直接调用另一个成员，可以省略this前缀。上面的this.jump();等价于jump();
* **static修饰的方法中不能使用this引用**，由于static修饰的方法不能使用this引用，所以static修饰的方法不能访问不使用static修饰的普通成员，因此Java语法规定：静态成员不能直接访问非静态成员。
* 调用成员变量、方法时，主调是必不可少的，即使省略了主调，实际的主调依然存在。**调用静态成员省略主调，默认使用该类作为主调**；**调用非静态成员省略主调，默认使用this作为主调**。
* this在构造器中引用该构造器正在初始化的对象。大部分时候，在构造器中访问其他成员变量和方法时都可以省略this前缀，但如果构造器中有一个和成员变量同名的局部变量，又必须在构造器中访问这个被覆盖的成员变量，则必须使用this前缀。
```java
public class ThisInConstructor
{
	// 定义一个名为foo的成员变量
	public int foo;
	public ThisInConstructor()
	{
		// 在构造器里定义一个foo变量
		int foo = 0;
		// 使用this代表该构造器正在初始化的对象
		// 下面的代码将会把该构造器正在初始化的对象的foo成员变量设为6。
		this.foo = 6;
	}
	public static void main(String[] args)
	{
		// 所有使用ThisInConstructor创建的对象的foo成员变量
		// 都将被设为6，所以下面代码将输出6。
		System.out.println(new ThisInConstructor().foo);
	}
}
```

* 在某个方法中可以把this作为返回值，则可以多次连续调用同一个方法，从而使得代码更加简洁。但是使用这种把this作为返回值的方法可能造成实际意义的模糊。
```java
public class ReturnThis
{
	public int age;
	public ReturnThis grow()
	{
		age++;
		// return this返回调用该方法的对象
		return this;
	}
	public static void main(String[] args)
	{
		var rt = new ReturnThis();
		// 可以连续调用同一个方法
		rt.grow()
			.grow()
			.grow();
		System.out.println("rt的age成员变量值是:" + rt.age);
	}
}
```

* Java里方法的参数传递方式只有一种：**值传递**，即使是引用类型的参数，也是复制一个引用类型过去，只是这两个引用都指向同一个对象而已。
* 形参个数可变的方法，在最后一个形参的类型后增加三点（...），则表明该形参可以接受多个参数值，多个参数值被当成数组传入（所以可以用数组形式的参数代替）。数组形式的参数可以处于形参列表的任意位置，但个数可变的形参只能处于形参列表的最后，也就是一个方法中最多只能有一个个数可变的形参。
```java
public class Varargs
{
	// 定义了形参个数可变的方法
	public static void test(int a, String... books)
	{
		// books被当成数组处理
		for (var tmp : books)
		{
			System.out.println(tmp);
		}
		// 输出整数变量a的值
		System.out.println(a);
	}
	public static void main(String[] args)
	{
		// 调用test方法
		test(5, "疯狂Java讲义", "轻量级Java EE企业应用实战");
	}
}
```

* 递归一定要向已知方向递归（即朝着边界值的方法递归）
* 方法重载（方法名相同，但形参列表不同）
```java
public class OverloadVarargs
{
	public void test(String msg)
	{
		System.out.println("只有一个字符串参数的test方法 ");
	}
	// 因为前面已经有了一个test()方法，test()方法里有一个字符串参数。
	// 此处的个数可变形参里不包含一个字符串参数的形式
	public void test(String... books)
	{
		System.out.println("****形参个数可变的test方法****");
	}
	public static void main(String[] args)
	{
		var olv = new OverloadVarargs();
		// 下面两次调用将执行第二个test()方法
		olv.test();
		olv.test("aa", "bb");
		// 下面将执行第一个test()方法
		olv.test("aa");
		// 下面调用将执行第二个test()方法（只有一个形参，为了调用第二个必须搞成数组形式）
		olv.test(new String[] {"aa"});
	}
}
```

* 与成员变量不同的是，局部变量除形参外，都必须显示初始化。也就是说，必须先给方法局部变量和代码块局部变量指定初始值，否则不可以访问它们。

![image-2](/assets/blogImg/CrazyJava2.png)

* 一个类里不能定义两个同名的成员变量，即使一个是类变量，一个是实例变量也不行；一个方法里不能定义两个同名的方法局部变量，方法局部变量与形参也不能同名；**如果方法里的局部变量和成员变量同名，局部变量会覆盖成员变量，如果需要在这个方法里引用被覆盖的成员变量，则可以使用this（对于实例变量）或类名（对于类变量）作为调用者来限定访问成员变量**。
```java
public class VariableOverrideTest
{
	// 定义一个name实例变量
	private String name = "李刚";
	// 定义一个price类变量
	private static double price = 78.0;
	// 主方法，程序的入口
	public static void main(String[] args)
	{
		// 方法里的局部变量，局部变量覆盖成员变量
		var price = 65;
		// 直接访问price变量，将输出price局部变量的值：65
		System.out.println(price);
		// 使用类名作为price变量的限定，
		// 将输出price类变量的值：78.0
		System.out.println(VariableOverrideTest.price);
		// 运行info方法
		new VariableOverrideTest().info();
	}
	public void info()
	{
		// 方法里的局部变量，局部变量覆盖成员变量
		var name = "孙悟空";
		// 直接访问name变量，将输出name局部变量的值："孙悟空"
		System.out.println(name);
		// 使用this来作为name变量的限定，
		// 将输出name实例变量的值："李刚"
		System.out.println(this.name);
	}
}
```

* 局部变量不属于任何类或实例，因此它总是保存在其所在方法的栈内存中。栈内存中的变量无需系统垃圾回收，往往随方法或代码块的运行结束而结束，局部变量只保存基本类型的值或者对象的引用，因此局部变量所占的内存区通常比较小。在程序中使用局部变量，也应该尽可能地缩小局部变量的作用范围。
* **访问控制符**实现**封装**
  * private（**当前类**访问权限）：如果类里的一个成员使用private修饰，则它只能在当前类的内部被访问。
  * default（**包**访问权限）：不使用任何访问控制符修饰，成员或外部类可以被相同包下的其他类访问。
  * protected（**子类**访问权限）：如果一个成员使用protected修饰，可以被不同包中的子类访问。使用它通常是希望其子类来重写这个方法。
  * public（**公共**访问权限）：成员或外部类可以被所有类访问。

|            | private | default | protected | public |
| :--------- | :-----: | :-----: | :-------: | :----: |
| 同一个类中 |    √    |    √    |     √     |   √    |
| 同一个包中 |         |    √    |     √     |   √    |
| 子类中     |         |         |     √     |   √    |
| 全局范围   |         |         |           |   √    |

* 外部类只能有两种访问控制级别：public和默认（default）

```java
public class Person
{
	// 使用private修饰成员变量，将这些成员变量隐藏起来
	private String name;
	private int age;
	// 提供方法来操作name成员变量
	public void setName(String name)
	{
		// 执行合理性校验，要求用户名必须在2～6位之间
		if (name.length() > 6 || name.length() < 2)
		{
			System.out.println("您设置的人名不符合要求");
			return;
		}
		else
		{
			this.name = name;
		}
	}
	public String getName()
	{
		return this.name;
	}
	// 提供方法来操作age成员变量
	public void setAge(int age)
	{
		// 执行合理性校验，要求用户年龄必须在0～100之间
		if (age > 100 || age < 0)
		{
			System.out.println("您设置的年龄不合法");
			return;
		}
		else
		{
			this.age = age;
		}
	}
	public int getAge()
	{
		return this.age;
	}
}
文件PersonTest.java
public class PersonTest
{
	public static void main(String[] args)
	{
		var p = new Person();
		// 因为age成员变量已被隐藏，所以下面语句将出现编译错误。
		// p.age = 1000;
		// 下面语句编译不会出现错误，但运行时将提示"您设置的年龄不合法"
		// 程序不会修改p的age成员变量
		p.setAge(1000);
		// 访问p的age成员变量也必须通过其对应的getter方法
		// 因为上面从未成功设置p的age成员变量，故此处输出0
		System.out.println("未能设置age成员变量时："
			+ p.getAge());
		// 成功修改p的age成员变量
		p.setAge(30);
		// 因为上面成功设置了p的age成员变量，故此处输出30
		System.out.println("成功设置age成员变量后："
			+ p.getAge());
		// 不能直接操作p的name成员变量，只能通过其对应的setter方法
		// 因为"李刚"字符串长度满足2~6,所以可以成功设置
		p.setName("李刚");
		System.out.println("成功设置name成员变量后："
			+ p.getName());
	}
}
```

* package、import、import static
```java
package lee; // package语句必须作为源文件的第一条非注释性语句，一个源文件只能指定一个包
public class Hello
{
	public static void main(String[] args)
	{
		System.out.println("Hello World!");
	}
}
// 使用 javac -d . Hello.java 编译，会生成lee文件夹。使用 java lee.Hello运行
```

* import语句应该出现在package语句（如果有）之后、类定义之前。一个源文件只能包含一个package语句，但可以包含多个import语句，import语句中的\*号只能代表类（所有类），不能代表包
* Java默认为所有源文件导入java.lang包下的所有类，因此使用String、System类时都无需使用import语句。
* 静态导入（import static），用于导入指定类的某个静态成员变量、方法或全部的静态成员变量、方法。使用**import可以省略写包名，而使用import static则可以连类名都省略**。

```java
import static java.lang.System.*;
import static java.lang.Math.*;

public class StaticImportTest
{
	public static void main(String[] args)
	{
		// out是java.lang.System类的静态成员变量，代表标准输出
		// PI是java.lang.Math类的静态成员变量，表示π常量
		out.println(PI);
		// 直接调用Math类的sqrt静态方法
		out.println(sqrt(256));
	}
}
```

* 方法B完全包含方法A，则可以在方法B中调用方法A。同样的情况构造器则不能直接被调用，构造器必须使用new关键字来调用。但一旦使用new，将会导致系统重新创建一个对象。为了在构造器B中调用构造器A中的初始化代码，又不会重新创建一个对象，可以使用this关键字来调用相应的构造器。**使用this调用另一个重载的构造器只能在构造器中使用，而且必须作为构造器执行体的第一条语句。系统会根据this后括号里的实参来调用形参列表与之对应的构造器**。
```java
public class Apple
{
	public String name;
	public String color;
	public double weight;
	public Apple(){}
	// 两个参数的构造器
	public Apple(String name, String color)
	{
		this.name = name;
		this.color = color;
	}
	// 三个参数的构造器
	public Apple(String name, String color, double weight)
	{
		// 通过this调用另一个重载的构造器的初始化代码
		this(name, color);
		// 下面this引用该构造器正在初始化的Java对象
		this.weight = weight;
	}
}
```

* 类的继承：extends关键字，Java的继承是**单继承，每个子类只有一个直接父类（间接父类可以有多个）**
* 子类只能从被继承的父类获得成员变量、方法和内部类（包括内部接口、枚举），不能获得构造器和初始化块
* 如果定义了一个Java类时并未显式指定这个类的直接父类，则这个类默认扩展java.lang.Object类。因此，**java.lang.Object类是所有类的父类，要么是其直接父类，要么是其间接父类**。因此所有的对象都可调用java.lang.Object类所定义的实例方法。
* 方法**重写**（Override），也被称为方法覆盖。子类重写（覆盖）了父类的方法。

  * **两同**：方法名相同、形参列表相同。
  * **两小**：子类方法返回值类型应比父类方法返回值类型更小或相等，子类方法声明抛出的异常类应比父类方法声明抛出的异常类更小或相等。
  * **一大**：子类方法的访问权限应比父类方法的访问权限更大或相等。
  * 覆盖方法和被覆盖方法要么都是类方法，要么都是实例方法。
* 如果需要在子类方法中调用父类被覆盖的方法，则可以使用**super**（被覆盖的是实例方法）或**父类类名**（被覆盖的是类方法）作为调用者来调用父类中被覆盖的方法。**正如this不能出现在static修饰的方法中一样，super也不能出现在static修饰的方法中**。
* 如果在某个方法中访问名为a的成员变量，但没有显式指定调用者，则系统查找a的顺序为：

  1. 查找该方法中是否有名为a的局部变量。

  2. 查找当前类中是否包含名为a的成员变量。

  3. 查找a的直接父类中是否包含名为a的成员变量，依次上溯a的所有父类，直到java.lang.Object类，如果最终不能找到名为a的成员变量，则系统出现编译错误。
* 在一个构造器中调用另一个重载的构造器使用this调用来完成，在子类构造器中调用父类构造器使用super调用来完成。使用super调用父类构造器也必须出现在子类构造器执行体的第一行，所以**this调用和super调用不会同时出现**。
* **子类构造器总会调用父类构造器一次**（即没有显示super调用父类构造器，也没有this调用本类重载的构造器，系统会在执行子类构造器之前，隐式调用父类无参数的构造器）。因为当调用子类构造器来初始化子类对象时，父类构造器总会在子类构造器之前执行；不仅如此，执行父类构造器时，系统会再次上溯执行其父类构造器.....依此类推，创建任何对象，最先执行的总是java.lang.Object类的构造器。
* 引用变量有两个类型：一个是编译时类型，一个是运行时类型。**如果编译时类型和运行时类型不一致**，就可能出现所谓的**多态**（Polymorphism）。相同类型的变量、调用同一个方法时呈现出多种不同的行为特征，这就时多态。与方法不同的是，对象的实例变量则不具备多态性。
```java
class BaseClass
{
	public int book = 6;
	public void base()
	{
		System.out.println("父类的普通方法");
	}
	public void test()
	{
		System.out.println("父类的被覆盖的方法");
	}
}
public class SubClass extends BaseClass
{
	//重新定义一个book实例变量隐藏父类的book实例变量
	public String book = "轻量级Java EE企业应用实战";
	public void test()
	{
		System.out.println("子类的覆盖父类的方法");
	}
	public void sub()
	{
		System.out.println("子类的普通方法");
	}
	public static void main(String[] args)
	{
		// 下面编译时类型和运行时类型完全一样，因此不存在多态
		BaseClass bc = new BaseClass();
		// 输出 6
		System.out.println(bc.book);
		// 下面两次调用将执行BaseClass的方法
		bc.base();
		bc.test();
		// 下面编译时类型和运行时类型完全一样，因此不存在多态
		SubClass sc = new SubClass();
		// 输出"轻量级Java EE企业应用实战"
		System.out.println(sc.book);
		// 下面调用将执行从父类继承到的base()方法
		sc.base();
		// 下面调用将执行从当前类的test()方法
		sc.test();
		// 下面编译时类型和运行时类型不一样，多态发生
		BaseClass ploymophicBc = new SubClass();
		// 输出6 ―― 表明访问的是父类对象的实例变量
		System.out.println(ploymophicBc.book);  // 实例变量不具有多态性
		// 下面调用将执行从父类继承到的base()方法
		ploymophicBc.base();
		// 下面调用将执行从当前类的test()方法（多态：明明是BaseClass，却执行的是SubClass的test方法）
		ploymophicBc.test();
		// 因为ploymophicBc的编译类型是BaseClass，
		// BaseClass类没有提供sub方法,所以下面代码编译时会出现错误。
		// ploymophicBc.sub();


		// 编译器推断v1是SubClass类型
		var v1 = new SubClass();
		// 由于ploymophicBc的编译时类型是BaseClass
		// 因此编译器推断v2是BaseClass类型
		var v2 = ploymophicBc;
		// 由于BaseClass类没有提供sub方法,所以下面代码编译时会出现错误。
		v2.sub();
	}
}
```

* 子类其实是一种特殊的父类，因此Java允许把子类对象直接赋给一个父类引用变量，无须任何类型转换，或者被称为**向上转换**（upcasting），向上转换由系统自动完成。
* **引用变量在编译阶段只能调用其编译时类型所具有的方法，但运行时则执行它运行时类型所具有的的方法**。因此，编写Java代码时，引用变量只能调用声明该变量时所用类型里包含的方法。
* 通过引用变量来访问其包含的**实例变量**时，系统总是试图访问它**编译时类型所定义的成员变量**，而不是它运行时类型所定义的成员变量。（实例变量不具有多态）
* 基本类型之间的转换只能在数值类型之间进行。引用类型之间的转换只能在具有继承关系的两个类型之间进行，在进行强制类型转换之前，先用**instanceof**运算符判断是否可以成功转换，从而避免出现ClassCastException异常，这样可以保证程序更加健壮。
* instanceof运算符用于**判断前面的对象是否是后面的类，或者其子类、实现类的实例**，如果是返回true，不是返回false。instanceof前面操作数的编译时类型要么与后面的类型相同，要么与后面的类具有父子继承关系，否则会引起编译错误。通常先用instanceof判断一个对象是否可以强制类型转换，然后再使用（type）运算符进行强制类型转换，从而保证程序不会出现错误。
```java
public class InstanceofTest
{
	public static void main(String[] args)
	{
		// 声明hello时使用Object类，则hello的编译类型是Object，
		// Object是所有类的父类, 但hello变量的实际类型是String
		Object hello = "Hello";
		// String与Object类存在继承关系，可以进行instanceof运算。返回true。
		System.out.println("字符串是否是Object类的实例："
			+ (hello instanceof Object));
		System.out.println("字符串是否是String类的实例："
			+ (hello instanceof String)); // 返回true。
		// Math与Object类存在继承关系，可以进行instanceof运算。返回false。
		System.out.println("字符串是否是Math类的实例："
			+ (hello instanceof Math));
		// String实现了Comparable接口，所以返回true。
		System.out.println("字符串是否是Comparable接口的实例："
			+ (hello instanceof Comparable));
		var a = "Hello";
		// String类与Math类没有继承关系，所以下面代码编译无法通过
		// System.out.println("字符串是否是Math类的实例："
		//	+ (a instanceof Math));
	}
}
```

* 继承严重地破坏了父类的封装性，造成子类与父类的严重耦合。为了保证父类有良好的封装性，不会被子类随意改变，设计父类通常应该遵循如下规则：
  * 尽量隐藏父类的内部数据，即设置成private访问类型。
  * 不要让子类可以随意访问、修改父类的方法。父类中那些仅为辅助其他的工具方法，应该使用private访问控制符修饰，让子类无法访问该方法；如果父类中的方法需要被外部类调用，则必须以public修饰，但又不希望子类重写该方法，可以使用**final**修饰（把类设置成最终类，即不能被当成父类，例如JDK提供的java.lang.String类和java.lang.System类）；如果希望父类的某个方法被子类重写，但不希望被其他类自由访问，则可以使用protected来修饰。
  * 尽量不要在父类构造器中调用将要被子类重写的方法。
* 只是为了实现类复用，并不一定需要采用继承。可以考虑采用**组合**方式，能提供更好的封装性。一般在子类需要额外增加成员变量或增加自己独有的行为方式（包括增加新的方法或重写父类的方法）时采用继承。继承设计与组合设计的系统开销不会有本质的差别。
* 组合是把旧类对象作为新类的成员变量组合进来，用以实现新类的功能，用户看到的是新类的方法，而不能看到被组合对象的方法。因此通常需要在新类使用private修饰被组合的旧类对象。
```java
class Animal
{
	private void beat()
	{
		System.out.println("心脏跳动...");
	}
	public void breathe()
	{
		beat();
		System.out.println("吸一口气，吐一口气，呼吸中...");
	}
}
class Bird
{
	// 将原来的父类组合到原来的子类，作为子类的一个组合成分
	private Animal a;
	public Bird(Animal a)
	{
		this.a = a;
	}
	// 重新定义一个自己的breathe()方法
	public void breathe()
	{
		// 直接复用Animal提供的breathe()方法来实现Bird的breathe()方法。
		a.breathe();
	}
	public void fly()
	{
		System.out.println("我在天空自在的飞翔...");
	}
}
class Wolf
{
	// 将原来的父类组合到原来的子类，作为子类的一个组合成分
	private Animal a;
	public Wolf(Animal a)
	{
		this.a = a;
	}
	// 重新定义一个自己的breathe()方法
	public void breathe()
	{
		// 直接复用Animal提供的breathe()方法来实现Wolf的breathe()方法。
		a.breathe();
	}
	public void run()
	{
		System.out.println("我在陆地上的快速奔跑...");
	}
}
public class CompositeTest
{
	public static void main(String[] args)
	{
		// 此时需要显式创建被组合的对象
		var a1 = new Animal();
		var b = new Bird(a1);
		b.breathe();
		b.fly();
		// 此时需要显式创建被组合的对象
		var a2 = new Animal();
		var w = new Wolf(a2);
		w.breathe();
		w.run();
	}
}
```

* **初始化块**是Java类里可出现的第4种成员，一个类里可以有多个初始化块，相同类型的初始化块之间有顺序：前面定义的初始化块先执行。**初始化块的修饰符只能出现static**。初始化块没有名字，也就没有标识，因此无法通过类、对象来调用初始化块。**实例初始化块只在创建Java对象时隐式执行，而且在构造器执行之前自动执行。类初始化块则在类初始化阶段自动执行**。
* 实际上**实例初始化块是一个假象**，使用javac命令编译Java类后，该类中的实例初始化块会消失——实例初始化块中代码**会被“还原“到每个构造器中，且位于构造器所有代码的前面**。

```java
class Root
{
	static{
		System.out.println("Root的类初始化块");
	}
	{
		System.out.println("Root的实例初始化块");
	}
	public Root()
	{
		System.out.println("Root的无参数的构造器");
	}
}
class Mid extends Root
{
	static{
		System.out.println("Mid的类初始化块");
	}
	{
		System.out.println("Mid的实例初始化块");
	}
	public Mid()
	{
		System.out.println("Mid的无参数的构造器");
	}
	public Mid(String msg)
	{
		// 通过this调用同一类中重载的构造器
		this();
		System.out.println("Mid的带参数构造器，其参数值："
			+ msg);
	}
}
class Leaf extends Mid
{
	static{
		System.out.println("Leaf的类初始化块");
	}
	{
		System.out.println("Leaf的实例初始化块");
	}
	public Leaf()
	{
		// 通过super调用父类中有一个字符串参数的构造器
		super("疯狂Java讲义");
		System.out.println("执行Leaf的构造器");
	}
}
public class Test
{
	public static void main(String[] args)
	{
		new Leaf();
		new Leaf();
	}
}
Compiling Test.java......
------Output------
Root的类初始化块
Mid的类初始化块
Leaf的类初始化块   // 类初始化阶段，先执行最顶层父类的类初始化块，然后依次向下。
Root的实例初始化块
Root的无参数的构造器
Mid的实例初始化块
Mid的无参数的构造器
Mid的带参数构造器，其参数值：疯狂Java讲义
Leaf的实例初始化块
执行Leaf的构造器   // 对象初始化前，先执行最顶层父类的实例初始化块、最顶层父类的构造器，然后依次向下。
Root的实例初始化块
Root的无参数的构造器
Mid的实例初始化块
Mid的无参数的构造器
Mid的带参数构造器，其参数值：疯狂Java讲义
Leaf的实例初始化块
执行Leaf的构造器//一旦Leaf类初始化成功后，Leaf类一直在虚拟机中存在，第二次创建Leaf实例无须再次类初始化
```

### Unit 6  面向对象（下）

* **final**关键字修饰变量、方法和类，系统不允许为final变量重新赋值，子类不允许覆盖父类的final方法，final类不能派生子类。
* JDK1.5提供**自动装箱**（可以把一个基本类型变量直接赋值给对应的包装类变量，或者赋值给Object变量）和**自动拆箱**（允许直接把包装类对象直接赋给一个对应的基本类型变量）
* 借助于包装类的帮助，再加上自动装箱、自动拆箱功能，开发者可以把基本类型的变量“近似”地当成对象的使用（所有装箱、拆箱过程由系统自动完成，无需程序员理会）；反过来也可以把包装类的实例近似地当成基本类型的变量使用。包装类还可以实现基本类型变量和字符串之间的转换：**parseXxx(String s)**静态方法、**valueOf(String s)**静态方法，两者都用于将字符串类型转换为基本类型。String类提供了多个重载valueOf()方法，用于将基本类型变量转换成字符串。
```java
public class Primitive2String
{
	public static void main(String[] args)
	{
		var intStr = "123";
		// 把一个特定字符串转换成int变量
		var it1 = Integer.parseInt(intStr);
		var it2 = Integer.valueOf(intStr);
		System.out.println(it2);
		var floatStr = "4.56";
		// 把一个特定字符串转换成float变量
		var ft1 = Float.parseFloat(floatStr);
		var ft2 = Float.valueOf(floatStr);
		System.out.println(ft2);
		// 把一个float变量转换成String变量
		var ftStr = String.valueOf(2.345f);
		System.out.println(ftStr);
		// 把一个double变量转换成String变量
		var dbStr = String.valueOf(3.344);
		System.out.println(dbStr);
		// 把一个boolean变量转换成String变量
		var boolStr = String.valueOf(true);
		System.out.println(boolStr.toUpperCase());
	}
}
```

* Java的Integer类的设计，系统把一个\-128\~127之间的整数自动装箱成Integer实例，并放入了一个名为cache的数组中缓存起来。如果以后把一个\-128\~127之间的整数自动装箱成一个Integer实例时，实际上是直接指向对应的数组元素；但每次把一个不在-128~127范围内的整数自动装箱成Integer实例时，系统总是重新创建一个Integer实例。
```java
public class WrapperClassCompare
{
	public static void main(String[] args)
	{
		var a = Integer.valueOf(6);
		// 输出true
		System.out.println("6的包装类实例是否大于5.0" + (a > 5.0));
		System.out.println("比较2个包装类的实例是否相等："
			+ (Integer.valueOf(2) == Integer.valueOf(2))); // 输出true
		System.out.println("比较128个包装类的实例是否相等："
			+ (Integer.valueOf(128) == Integer.valueOf(128))); // 输出false
		// 通过自动装箱，允许把基本类型值赋值给包装类的实例
		Integer ina = 2;
		Integer inb = 2;
		System.out.println("两个2自动装箱后是否相等："
			+ (ina == inb)); // 输出true
		Integer biga = 128;
		Integer bigb = 128;
		System.out.println("两个128自动装箱后是否相等："
			+ (biga == bigb)); // 输出false
	}
}
```

* 包装类都提供了一个静态的**compare(xxx val1, xxx val2)**方法，可以用其比较两个基本类型值的大小。Java7还为Character包装类增加了大量的工具方法来对一个字符串进行判断，详细请参考API文档。Java8为整型包装类增加了**支持无符号运算**的方法，还为Byte、Short增加了**toUnsignedInt(xxx x)、toUnsignedLong(yyy x)**两个方法，用于将指定byte或short类型的变量或值转化成无符号的int或long值。
```java
public class UnsignedTest
{
	public static void main(String[] args)
	{
		byte b = -3;
		// 将byte类型的-3转换为无符号整数。
		System.out.println("byte类型的-3对应的无符号整数："
			+ Byte.toUnsignedInt(b)); // 输出253
		// 指定使用16进制解析无符号整数
		var val = Integer.parseUnsignedInt("ab", 16);
		System.out.println(val); // 输出171
		// 将-12转换为无符号int型，然后转换为16进制的字符串
		System.out.println(Integer.toUnsignedString(-12, 16)); // 输出fffffff4
		// 将两个数转换为无符号整数后相除
		System.out.println(Integer.divideUnsigned(-2, 3));
		// 将两个数转换为无符号整数相除后求余
		System.out.println(Integer.remainderUnsigned(-2, 7));
	}
}
```

* 打印对象和toString方法。System.out的println()方法只能在控制台输出字符串，而Person实例是一个内存中的对象，当使用该方法输出Person对象时，实际上输出的是Person对象的toString方法的返回值。**Object类提供的toString方法总是返回该对象实现类的“类名+@+hashCode”值**，我们可以**重写Object类的toString方法来输出一些我们感兴趣的东西**。
```java
class Person
{
	private String name;
	public Person(String name)
	{
		this.name = name;
	}
}
public class PrintObject
{
	public static void main(String[] args)
	{
		// 创建一个Person对象，将之赋给p变量
		var p = new Person("孙悟空");
		// 打印p所引用的Person对象,输出Person@512ddf17
		System.out.println(p);
	}
}
```

```java
class Apple
{
	private String color;
	private double weight;
	public Apple(){	}
	//提供有参数的构造器
	public Apple(String color, double weight)
	{
		this.color = color;
		this.weight = weight;
	}

	// color的setter和getter方法
	public void setColor(String color)
	{
		this.color = color;
	}
	public String getColor()
	{
		return this.color;
	}

	// weight的setter和getter方法
	public void setWeight(double weight)
	{
		this.weight = weight;
	}
	public double getWeight()
	{
		return this.weight;
	}

	// 重写toString方法，用于实现Apple对象的"自我描述"
	// public String toString()
	// {
	// 	return "一个苹果，颜色是：" + color
	// 		+ "，重量是：" + weight;
	// }

	public String toString()
	{
		return "Apple[color=" + color + ",weight=" + weight + "]";
	}

}
public class ToStringTest
{
	public static void main(String[] args)
	{
		var a = new Apple("红色", 5.68);
		// 打印Apple对象，输出Apple[color=红色,weight=5.68]
		System.out.println(a);
	}
}
```

* **==和equals**方法。使用==来判断两个变量是否相等时，如果两个变量是基本类型且都是数值类型(不一定要求数据类型严格相同)，则只要两个变量的值相等，就将返回true。但对于引用类型变量，只有它们指向同一个对象时，==判断才会返回true。==不可用于比较类型上没有父子关系的两个对象。
```java
public class EqualTest
{
	public static void main(String[] args)
	{
		var it = 65;
		var fl = 65.0f;
		// 将输出true
		System.out.println("65和65.0f是否相等？" + (it == fl));
		var ch = 'A';
		// 将输出true
		System.out.println("65和'A'是否相等？" + (it == ch));
		var str1 = new String("hello");
		var str2 = new String("hello");
		// 将输出false
		System.out.println("str1和str2是否相等？"
			+ (str1 == str2));
		// 将输出true
		System.out.println("str1是否equals str2？"
			+ (str1.equals(str2)));
		// 由于java.lang.String与EqualTest类没有继承关系，
		// 所以下面语句导致编译错误
		// System.out.println("hello" == new EqualTest());
	}
}
```

* **"hello"直接量**和**new String("hello")**的区别。使用字符串常量值，JVM将会使用常量池来管理这些字符串；当使用new String("hello")时，JVM先使用常量池（专门用于管理在编译时被确定并被保存在已编译的.class文件中的一些数据。它包括了关于类、方法、接口中的常量，还包括字符串常量）来管理"hello"直接量，再调用String类的构造器来创建一个新的String对象，新创建的String对象被保存在堆内存中。
```java
public class StringCompareTest
{
	public static void main(String[] args)
	{
		// s1直接引用常量池中的"疯狂Java"
		var s1 = "疯狂Java";
		var s2 = "疯狂";
		var s3 = "Java";
		// s4后面的字符串值可以在编译时就确定下来
		// s4直接引用常量池中的"疯狂Java"
		var s4 = "疯狂" + "Java";
		// s5后面的字符串值可以在编译时就确定下来
		// s5直接引用常量池中的"疯狂Java"
		var s5 = "疯" + "狂" + "Java";
		// s6后面的字符串值不能在编译时就确定下来，
		// 不能引用常量池中的字符串
		var s6 = s2 + s3;
		// 使用new调用构造器将会创建一个新的String对象，
		// s7引用堆内存中新创建的String对象
		var s7 = new String("疯狂Java");
		System.out.println(s1 == s4); // 输出true
		System.out.println(s1 == s5); // 输出true
		System.out.println(s1 == s6); // 输出false
		System.out.println(s1 == s7); // 输出false
	}
}
```

* **String对象的equals()方法重写了Object的equals()**，只要它们引用字符串对象里包含的字符序列相同即可认为相等。**Object的equals()和使用==运算符没有区别，同样要求两个引用变量指向同一个对象才会返回ture**。因此如果希望采用自定义的相等标准，可以**重写equals方法**。正确的重写equals应满足以下条件：
  * 自反性：x.equals(x)一定返回true
  * 对称性：任意x，y，如果y.equals(x)返回true，则x.equals(y)也返回true
  * 传递性：x.equals(y)返回true，y.equals(z)返回true，则x.equals(z)一定返回true
  * 一致性：只要对象中用于等价比较的信息没有改变，那么无论调用多少次x.equals(y)，返回结果一致。
  * 对于任何不是null的x，x.equals(null)一定返回false

```java
class Person
{
	private String name;
	private String idStr;
	public Person(){}
	public Person(String name, String idStr)
	{
		this.name = name;
		this.idStr = idStr;
	}
	// name的setter和getter方法
	public void setName(String name)
	{
		this.name = name;
	}
	public String getName()
	{
		return this.name;
	}
	// idStr的setter和getter方法
	public void setIdStr(String idStr)
	{
		this.idStr = idStr;
	}
	public String getIdStr()
	{
		return this.idStr;
	}
	// 重写equals()方法，提供自定义的相等标准
	public boolean equals(Object obj)
	{
		// 如果两个对象为同一个对象
		if (this == obj)
			return true;
		// 只有当obj是Person对象
		if (obj != null && obj.getClass() == Person.class)
		{
			var personObj = (Person) obj;
			// 并且当前对象的idStr与obj对象的idStr相等才可判断两个对象相等
			if (this.getIdStr().equals(personObj.getIdStr()))
			{
				return true;
			}
		}
		return false;
	}
}
public class OverrideEqualsRight
{
	public static void main(String[] args)
	{
		var p1 = new Person("孙悟空", "12343433433");
		var p2 = new Person("孙行者", "12343433433");
		var p3 = new Person("孙悟饭", "99933433");
		// p1和p2的idStr相等，所以输出true
		System.out.println("p1和p2是否相等？"
			+ p1.equals(p2));
		// p2和p3的idStr不相等，所以输出false
		System.out.println("p2和p3是否相等？"
			+ p2.equals(p3));
	}
}
```

* 即使某个实例为null，它也可以访问它所属类的类成员。但是如果一个null对象访问实例成员，将会引发NullPointerException异常，因为null表明该实例根本不存在。
* 如果一个类始终只能创建一个实例，则这个类被称为**单例类**
```java
class Singleton
{
	// 使用一个类变量来缓存曾经创建的实例
	private static Singleton instance;
	// 将构造器使用private修饰，隐藏该构造器
	private Singleton(){}
	// 提供一个静态方法，用于返回Singleton实例
	// 该方法可以加入自定义的控制，保证只产生一个Singleton对象
	public static Singleton getInstance()
	{
		// 如果instance为null，表明还不曾创建Singleton对象
		// 如果instance不为null，则表明已经创建了Singleton对象，
		// 将不会重新创建新的实例
		if (instance == null)
		{
			// 创建一个Singleton对象，并将其缓存起来
			instance = new Singleton();
		}
		return instance;
	}
}
public class SingletonTest
{
	public static void main(String[] args)
	{
		// 创建Singleton对象不能通过构造器，
		// 只能通过getInstance方法来得到实例
		Singleton s1 = Singleton.getInstance();
		Singleton s2 = Singleton.getInstance();
		System.out.println(s1 == s2); // 将输出true
	}
}
```

* **final**关键字可以修饰类、变量和方法。final修饰变量时，表示该变量一旦获得了初始值就不可以被改变，final修饰的成员变量必须由程序员显示地指定初始值：
  * 类变量：必须在**静态初始化块**或**声明该类变量时**指定初始值
  * 实例变量：必须在**非静态初始化块**、**声明该实例变量**或**构造器**中指定初始值
* 系统不会对局部变量进行初始化，局部变量必须由程序员显示初始化。**如果final修饰的局部变量在定义时没有指定默认值，则可以在后面代码中对该变量赋初始值，但只能一次**；如果定义时已经指定默认值，则后面代码不能再对该变量赋值。因为形参在调用方法时，由系统根据传入的参数来完成初始化，因此使用final修饰的形参不能被赋值。
* final修饰引用类型变量，只保证引用类型变量所引用的地址不会改变，即一直引用同一个对象，但这个对象完全可以发生改变。
* 可执行**“宏替换”**的final变量，满足三个条件，这个**final变量就不再是一个变量，而是相当于一个直接量**：
  * 使用final修饰符修饰
  * 在定义该final变量时指定了初始值
  * 该初始值可以在编译时就被确定下来
* 除了上面那种为final变量赋值时赋直接量的情况外，如果被赋的表达式只是基本的算术表达式或字符串连接运算，**没有访问普通变量，调用方法**，Java编译器同样会将这种final变量当成“宏变量”处理。
```java
public class FinalReplaceTest
{
	public static void main(String[] args)
	{
		// 下面定义了4个final“宏变量”
		final var a = 5 + 2;
		final var b = 1.2 / 3;
		final var str = "疯狂" + "Java";
		final var book = "疯狂Java讲义：" + 99.0;
		// 下面的book2变量的值因为调用了方法，所以无法在编译时被确定下来
		final var book2 = "疯狂Java讲义：" + String.valueOf(99.0); 
		System.out.println(book == "疯狂Java讲义：99.0");  // true
		System.out.println(book2 == "疯狂Java讲义：99.0"); // false
	}
}
```

```java
public class StringJoinTest
{
	public static void main(String[] args)
	{
		var s1 = "疯狂Java";
		// s2变量引用的字符串可以编译时就确定出来，
		// 因此s2直接引用常量池中已有的"疯狂Java"字符串
		var s2 = "疯狂" + "Java";
		System.out.println(s1 == s2); // 输出true
		// 定义2个字符串直接量
		var str1 = "疯狂";     // 由于str1、str2只是两个普通变量，编译器不会执行“宏替换”
		var str2 = "Java";    // 让s1==s3很简单，用final修饰str1和str2
		// 将str1和str2进行连接运算
		var s3 = str1 + str2;
		System.out.println(s1 == s3); // 输出false
	}
}
```

* final修饰的方法不可被重写，仅仅是不能被重写，并不是不能被重载。对于一个private修饰的方法，由于其子类无法访问该方法，所以子类定义一个与其有相同方法名、形参列表、返回值类型的方法是可以的，不过这不是方法重写，而是在子类中重新定义了一个方法。
* final修饰的类不可以有子类
* **不可变类的意思是创建该类的实例后，该实例的实例变量是不可改变的**。与不可变类对应的是可变类，可变类的含义是该类的实例变量的值是可变的。当使用**final修饰引用类型变量时，仅表示这个引用类型变量不可被重新赋值，但引用类型变量所指的对象依然可以改变**。这就产生了一个问题：当创建不可变类时，如果它包含的成员变量的类型是可变的，那么其对象的成员变量的值依然是可改变的——这个不可变类是失败的。**如果需要设计一个不可变类，尤其要注意其引用类型的成员变量，如果引用类型的成员变量的类是可变的，就必须采取必要的措施来保护该成员变量所引用的对象不会被修改，这样才能创建真正的不可变类**。
```java
class Name
{
	private String firstName;
	private String lastName;
	public Name(){}
	public Name(String firstName, String lastName)
	{
		this.firstName = firstName;
		this.lastName = lastName;
	}
	// firstName、lastName的setter和getter方法
	public void setFirstName(String firstName)
	{
		this.firstName = firstName;
	}
	public String getFirstName()
	{
		return this.firstName;
	}
	public void setLastName(String lastName)
	{
		this.lastName = lastName;
	}
	public String getLastName()
	{
		return this.lastName;
	}
}
public class Person
{
	private final Name name;
	public Person(Name name)
	{
//		this.name = name; 如果用这个，将导致Person实例变量name可变（引用对象），最后输出“八戒”
		// 设置name实例变量为临时创建的Name对象，该对象的firstName和lastName
		// 与传入的name参数的firstName和lastName相同
		this.name = new Name(name.getFirstName(), name.getLastName());
	}
	public Name getName()
	{
//		return name; 其实这里可以用这个（上面的必须新创建），因为此处的name已经是一个新的Name对象了
		// 返回一个匿名对象，该对象的firstName和lastName
		// 与该对象里的name的firstName和lastName相同
		return new Name(name.getFirstName(), name.getLastName());
	}
	public static void main(String[] args)
	{
		var n = new Name("悟空", "孙");
		var p = new Person(n);
		// Person对象的name的firstName值为"悟空"
		System.out.println(p.getName().getFirstName());
		// 改变Person对象name的firstName值
		n.setFirstName("八戒");
		System.out.println(n.getFirstName()); // “八戒”
		// p = new Person(n);
		// 只有再次构造，p才会输出“八戒”。不然始终是一开始的“悟空”
		System.out.println(p.getName().getFirstName());
	}
}
当程序向Person构造器里传入一个Name对象时，该构造器创建Person对象不是直接利用已有的Name对象（利用已有的Name对象有风险，因为其是可变的，如果程序改变了这个Name对象，将会导致Person对象也发生变化），而是重新创建了一个Name对象来赋值给Person对象的name实例变量。
```

* 缓存实例的不可变类：
```java
class CacheImmutale
{
	private static int MAX_SIZE = 10;
	// 使用数组来缓存已有的实例
	private static CacheImmutale[] cache
		= new CacheImmutale[MAX_SIZE];
	// 记录缓存实例在缓存中的位置,cache[pos-1]是最新缓存的实例
	private static int pos = 0;
	private final String name;
	private CacheImmutale(String name)
	{
		this.name = name;
	}
	public String getName()
	{
		return name;
	}
	public static CacheImmutale valueOf(String name)
	{
		// 遍历已缓存的对象，
		for (var i = 0; i < MAX_SIZE; i++)
		{
			// 如果已有相同实例，直接返回该缓存的实例
			if (cache[i] != null
				&& cache[i].getName().equals(name))
			{
				return cache[i];
			}
		}
		// 如果缓存池已满
		if (pos == MAX_SIZE)
		{
			// 把缓存的第一个对象覆盖，即把刚刚生成的对象放在缓存池的最开始位置。
			cache[0] = new CacheImmutale(name);
			// 把pos设为1
			pos = 1;
		}
		else
		{
			// 把新创建的对象缓存起来，pos加1
			cache[pos++] = new CacheImmutale(name);
		}
		return cache[pos - 1];

	}
	public boolean equals(Object obj)
	{
		if (this == obj)
		{
			return true;
		}
		if (obj != null && obj.getClass() == CacheImmutale.class)
		{
			var ci = (CacheImmutale) obj;
			return name.equals(ci.getName());
		}
		return false;
	}
	public int hashCode()
	{
		return name.hashCode();
	}
}
public class CacheImmutaleTest
{
	public static void main(String[] args)
	{
		var c1 = CacheImmutale.valueOf("hello");
		var c2 = CacheImmutale.valueOf("hello");
		// 下面代码将输出true
		System.out.println(c1 == c2);
	}
}
```

* Java提供的java.lang.Integer类，就采用了与CacheImmutale类相同的处理策略，如果**采用new构造器来创建Integer对象，则每次返回全新的Integer对象**（因为性能较差，Java9已标记为过时）；如果**采用valueOf()方法创建Integer对象，则会缓存该方法创建的对象**。
```java
public class IntegerCacheTest
{
	public static void main(String[] args)
	{
		// 生成新的Integer对象
		var in1 = new Integer(6);
		// 生成新的Integer对象，并缓存该对象
		var in2 = Integer.valueOf(6);
		// 直接从缓存中取出Ineger对象
		var in3 = Integer.valueOf(6);
		System.out.println(in1 == in2); // 输出false
		System.out.println(in2 == in3); // 输出true
		// 由于Integer只缓存-128~127之间的值，
		// 因此200对应的Integer对象没有被缓存。
		var in4 = Integer.valueOf(200);
		var in5 = Integer.valueOf(200);
		System.out.println(in4 == in5); //输出false
	}
}
```

* **抽象类**：抽象方法是只有方法签名，没有方法实现的方法。抽象方法和抽象类的规则如下：
  * 抽象类、方法必须使用abstract修饰，抽象方法**不能有方法体**
  * 抽象类**不能被实例化**，无法使用new来创造实例。抽象类的构造器主要是用于被其子类调用
  * **含有抽象方法的类只能被定义成抽象类**
* 利用抽象类和抽象方法的优势，可以更好地发挥多态的优势，使得程序更加灵活
```java
文件Shape.java
public abstract class Shape
{
	{
		System.out.println("执行Shape的初始化块...");
	}
	private String color;
	// 定义一个计算周长的抽象方法
	public abstract double calPerimeter();
	// 定义一个返回形状的抽象方法
	public abstract String getType();
	// 定义Shape的构造器，该构造器并不是用于创建Shape对象，
	// 而是用于被子类调用
	public Shape(){}
	public Shape(String color)
	{
		System.out.println("执行Shape的构造器...");
		this.color = color;
	}
	public void setColor(String color)
	{
		this.color = color;
	}
	public String getColor()
	{
		return this.color;
	}
}
文件Triangle.java
public class Triangle extends Shape
{
	// 定义三角形的三边
	private double a;
	private double b;
	private double c;
	public Triangle(String color, double a, double b, double c)
	{
		super(color);
		this.setSides(a, b, c);
	}
	public void setSides(double a, double b, double c)
	{
		if (a >= b + c || b >= a + c || c >= a + b)
		{
			System.out.println("三角形两边之和必须大于第三边");
			return;
		}
		this.a = a;
		this.b = b;
		this.c = c;
	}
	// 重写Shape类的的计算周长的抽象方法
	public double calPerimeter()
	{
		return a + b + c;
	}
	// 重写Shape类的的返回形状的抽象方法
	public String getType()
	{
		return getColor() + "三角形";
	}
}
文件Circle.java
public class Circle extends Shape
{
	private double radius;
	public Circle(String color, double radius)
	{
		super(color);
		this.radius = radius;
	}
	public void setRadius(double radius)
	{
		this.radius = radius;
	}
	// 重写Shape类的的计算周长的抽象方法
	public double calPerimeter()
	{
		return 2 * Math.PI * radius;
	}
	// 重写Shape类的的返回形状的抽象方法
	public String getType()
	{
		return getColor() + "圆形";
	}
	public static void main(String[] args)
	{
		Shape s1 = new Triangle("黑色", 3, 4, 5);
		Shape s2 = new Circle("黄色", 3);
		System.out.println(s1.getType());
		System.out.println(s1.calPerimeter());
		System.out.println(s2.getType());
		System.out.println(s2.calPerimeter());
	}
}
```

* 当使用abstract修饰类时，表明这个类只能被继承；当使用abstract修饰方法时，表明这个方法必须由子类提供实现（即重写）。而final修饰的类不能被继承，final修饰的方法不能被重写。因此两者永远不能同时使用
* 抽象类体现的就是一种**模板模式**（设计模式之一）的设计，抽象类作为多个子类的通用模板，子类在抽象类的基础上进行扩展、改造，但子类总体上会大致保留抽象类的行为方法。
* 使用模板模式的一些简单规则：
  * 抽象父类可以只定义需要使用的某些方法，把不能实现的部分抽象成抽象方法，留给其子类去实现。
  * 父类中可能包含需要调用其他系列方法的方法，这些被调用方法既可以由父类实现，也可以由其子类实现。父类里提供的方法只是定义了一个通用算法，其实现也许并不完全由自身实现，而必须依赖于其子类的辅助。
* **接口**定义了一种规范，**接口定义了某一批类所需要遵守的规范。接口是从多个相似类中抽象出来的规范**，接口不提供任何实现。接口体现的是规范和实例分离的设计哲学。
  * 修饰符可以使public或者省略（包权限访问）
  * 一个接口可以有多个直接父接口，但接口只能继承接口，不能继承类
* 接口里不能包含构造器和初始化块定义，接口里可以包含成员变量（只能是静态常量）、方法（只能是抽象实例方法、类方法、默认方法和私有方法）、内部类（包括内部接口、枚举）定义。
  * Java9新增的私有方法，主要作用就是作为工具方法，为接口中的默认方法或类方法提供支持。私有方法可以拥有方法体，但私有方法不能使用default修饰。私有方法可以使用static修饰，即私有方法既可以是类方法，也可是实例方法。
  * 对静态常量而言，系统自动为这些成员变量增加static和final两个修饰，即默认使用public static final
  * 如果不是定义默认方法、类方法或私有方法，系统将自动为普通方法增加abstract修饰符。接口里的普通方法不能有方法实现（方法体）；但类方法、默认方法、私有方法必须有方法实现（方法体）
  * 接口里定义的内部类、内部接口、内部枚举默认都采用public static两个修饰符
  * 默认方法必须使用default修饰，该方法不能使用static修饰，即默认使用public修饰。因此必须使用接口的实现类的实例来调用这些默认方法。(**其实默认方法就是有方法体的实例方法，普通方法是无方法体的实例方法**)
* 接口的继承支持多继承，即**一个接口可以有多个直接父接口**。接口的**实现**用**implements**关键字。**一个类可以实现多个接口**（这又是java为单继承灵活性不足的所做的补充，**类实现接口，模拟多继承**）。**一个类可以继承一个父类，并同时实现多个接口**，implements部分必须放在extends部分之后。实现接口可以获得所实现接口里定义的常量（成员变量）、方法（包括抽象方法和默认方法）
```java
public interface Output
{
	// 接口里定义的成员变量只能是常量
	int MAX_CACHE_LINE = 50;
	// 接口里定义的普通方法只能是public的抽象方法
	void out();
	void getData(String msg);
	// 在接口中定义默认方法，需要使用default修饰
	default void print(String... msgs)
	{
		for (var msg : msgs)
		{
			System.out.println(msg);
		}
	}
	// 在接口中定义默认方法，需要使用default修饰
	default void test()
	{
		System.out.println("默认的test()方法");
	}
	// 在接口中定义类方法，需要使用static修饰
	static String staticTest()
	{
		return "接口里的类方法";
	}
	// 定义私有方法
	private void foo()
	{
		System.out.println("foo私有方法");
	}
	// 定义私有静态方法
	private static void bar()
	{
		System.out.println("bar私有静态方法");
	}
}

interface Product
{
	int getProduceTime();
}
// 让Printer类实现Output和Product接口
public class Printer implements Output, Product
{
	private String[] printData
		= new String[MAX_CACHE_LINE];
	// 用以记录当前需打印的作业数
	private int dataNum = 0;
	public void out()
	{
		// 只要还有作业，继续打印
		while (dataNum > 0)
		{
			System.out.println("打印机打印：" + printData[0]);
			// 把作业队列整体前移一位，并将剩下的作业数减1，见P104
			System.arraycopy(printData, 1,
				printData, 0, --dataNum);
		}
	}
	public void getData(String msg)
	{
		if (dataNum >= MAX_CACHE_LINE)
		{
			System.out.println("输出队列已满，添加失败");
		}
		else
		{
			// 把打印数据添加到队列里，已保存数据的数量加1。
			printData[dataNum++] = msg;
		}
	}
	public int getProduceTime()
	{
		return 45;
	}
	public static void main(String[] args)
	{
		// 创建一个Printer对象，当成Output使用
		Output o = new Printer();
		o.getData("轻量级Java EE企业应用实战");
		o.getData("疯狂Java讲义");
		o.out();
		o.getData("疯狂Android讲义");
		o.getData("疯狂Ajax讲义");
		o.out();
		// 调用Output接口中定义的默认方法
		o.print("孙悟空", "猪八戒", "白骨精");
		o.test();
		// 创建一个Printer对象，当成Product使用
		Product p = new Printer();
		System.out.println(p.getProduceTime());
		// 所有接口类型的引用变量都可直接赋给Object类型的变量
		Object obj = p;
	}
}
```

* 接口不能显示继承任何类，但所有接口类型的引用变量都可以直接赋给Object类型的引用变量。因为任何对象都必须是Object或其子类的实例，Product类型的对象也不例外（它必须是Product接口实现类的对象，该实现类肯定是Object的显式或隐式子类）
* 接口和抽象类之间的差别：
  * 接口作为系统与外界交互的窗口，体现的是一种规范，类似于整个系统的“总纲”；抽象类作为系统中多个子类的共同父类，它所体现的是一种模板式设计，可以被当成是系统实现过程中的中间产品。
  * 接口里不能为普通方法提供方法实现，抽象类则完全可以包含有方法体的普通方法
  * 接口里只能定义静态常量，不能定义普通成员变量；抽象类则两者都可以定义
  * 接口里不包含构造器，而抽象类可以包含。抽象类里的构造器不是用于创建对象，而是让其子类调用这些构造器来完成属于抽象类的初始化操作
  * 接口里不包含初始化块；但抽象类则完全可以包含初始化块
  * 一个类最多只有一个直接父类，包括抽象类；但一个类可以直接实现多个接口，弥补了java单继承的不足
* **两种常用场景示范面向接口编程的优势**：
  
  * **简单工厂模式**：假设一个Computer类需要组合一个输出设备，组合一个Output要好于直接组合Printer，将Computer类与Printer类完全分离，则Computer对象实际组合的是Printer还是BetterPrinter对象就是完全透明。当Printer对象切换到BetterPrinter对象时，系统完全不受影响
  ```java
  文件 Output.java
  public interface Output
  {
  	// 接口里定义的成员变量只能是常量
  	int MAX_CACHE_LINE = 50;
  	// 接口里定义的普通方法只能是public的抽象方法
  	void out();
  	void getData(String msg);
  	// 在接口中定义默认方法，需要使用default修饰
  	default void print(String... msgs)
  	{
  		for (var msg : msgs)
  		{
  			System.out.println(msg);
  		}
  	}
  	// 在接口中定义默认方法，需要使用default修饰
  	default void test()
  	{
  		System.out.println("默认的test()方法");
  	}
  	// 在接口中定义类方法，需要使用static修饰
  	static String staticTest()
  	{
  		return "接口里的类方法";
  	}
  	// 定义私有方法
  	private void foo()
  	{
  		System.out.println("foo私有方法");
  	}
  	// 定义私有静态方法
  	private static void bar()
  	{
  		System.out.println("bar私有静态方法");
  	}
  }
  
  文件 Computer.java
  public class Computer
  {
  	private Output out;
  	public Computer(Output out)
  	{
  		this.out = out;
  	}
  	// 定义一个模拟获取字符串输入的方法
  	public void keyIn(String msg)
  	{
  		out.getData(msg);
  	}
  	// 定义一个模拟打印的方法
  	public void print()
  	{
  		out.out();
  	}
  }
  
  文件 OutputFactory.java
  public class OutputFactory
  {
  	public Output getOutput()
  	{
  //		return new Printer();
  		return new BetterPrinter();
  	}
  	public static void main(String[] args)
  	{
  		var of = new OutputFactory();
  		var c = new Computer(of.getOutput());
  		c.keyIn("轻量级Java EE企业应用实战");
  		c.keyIn("疯狂Java讲义");
  		c.print();
  	}
  }
  
  文件 Pinter.java
  // 让Printer类实现Output和Product接口
  public class Printer implements Output
  {
  	private String[] printData
  		= new String[MAX_CACHE_LINE];
  	// 用以记录当前需打印的作业数
  	private int dataNum = 0;
  	public void out()
  	{
  		// 只要还有作业，继续打印
  		while (dataNum > 0)
  		{
  			System.out.println("打印机打印：" + printData[0]);
  			// 把作业队列整体前移一位，并将剩下的作业数减1
  			System.arraycopy(printData, 1,
  				printData, 0, --dataNum);
  		}
  	}
  	public void getData(String msg)
  	{
  		if (dataNum >= MAX_CACHE_LINE)
  		{
  			System.out.println("输出队列已满，添加失败");
  		}
  		else
  		{
  			// 把打印数据添加到队列里，已保存数据的数量加1。
  			printData[dataNum++] = msg;
  		}
  	}
  }
  
  文件 BetterPrinter.java
  public class BetterPrinter implements Output
  {
  	private String[] printData
  		= new String[MAX_CACHE_LINE * 2];
  	// 用以记录当前需打印的作业数
  	private int dataNum = 0;
  	public void out()
  	{
  		// 只要还有作业，继续打印
  		while (dataNum > 0)
  		{
  			System.out.println("高速打印机正在打印：" + printData[0]);
  			// 把作业队列整体前移一位，并将剩下的作业数减1
  			System.arraycopy(printData, 1, printData, 0, --dataNum);
  		}
  	}
  	public void getData(String msg)
  	{
  		if (dataNum >= MAX_CACHE_LINE * 2)
  		{
  			System.out.println("输出队列已满，添加失败");
  		}
  		else
  		{
  			// 把打印数据添加到队列里，已保存数据的数量加1。
  			printData[dataNum++] = msg;
  		}
  	}
  }
  
  如果系统需要将Printer改为BetterPrinter实现类，只需让BetterPrinter实现Output接口，并改变OutputFactory类中的getOutput即可。通过这种方式，即可把所有生成Output对象的逻辑集中在OutputFactory工厂类中管理，而所有需要使用Output对象的类只需与Output接口耦合，而不是与具体的实现类耦合
  ```
  
  * **命令模式**：某个方法需要完成某一个行为，但这个行为的具体实现无法确定，必须等到执行该方法时才可以确定，可以考虑使用一个Command接口来定义一个方法，用这个方法来封装“处理行为”。
  ```java
  文件Command.java
  public interface Command
  {
  	// 接口里定义的process()方法用于封装“处理行为”
  	void process(int element);
  }
  文件ProcessArray.java
  public class ProcessArray
  {
  	public void process(int[] target, Command cmd)
  	{
  		for (var t : target)
  		{
  			cmd.process(t);
  		}
  	}
  }
  文件CommandTest.java
  public class CommandTest
  {
  	public static void main(String[] args)
  	{
  		var pa = new ProcessArray();
		int[] target = {3, -4, 6, 4};
  		// 第一次处理数组，具体处理行为取决于PrintCommand
  		pa.process(target, new PrintCommand());
  		System.out.println("------------------");
  		// 第二次处理数组，具体处理行为取决于SquareCommand
  		pa.process(target, new SquareCommand());
  	}
  }
  文件PrintCommand.java
  public class PrintCommand implements Command
  {
  	public void process(int element)
  	{
  		System.out.println("迭代输出目标数组的元素:" + element);
  	}
  }
  文件SquareCommand.java
  public class SquareCommand implements Command
  {
  	public void process(int element)
  	{
  		System.out.println("数组元素的平方是:" + element * element);
  	}
  }
  ```
  
* 定义在其他类内部的类被称为**内部类（嵌套类）**，包含内部类的也被称为**外部类（宿主类）**
  * 内部类提供了更好的封装，可以把内部类隐藏在外部类之内，不允许同一个包中的其他类访问该类
  * 内部类成员可以直接访问外部类的私有数据，但外部类不能访问内部类的实现细节
  * **匿名内部类适用于创建那些仅需要一次使用的类**。前面介绍的命令模式，定义PrintCommand和SquareCommand两个实现类可能没有太大的意义，因为这两个实现类可能仅需要适用一次。在这种情况下，适用匿名内部类将更方便
  * 内部类比外部类可以多使用三个修饰符：private、protected、static，非静态内部类不能拥有静态成员

* 定义在方法里的内部类被称为**局部内部类**。大部分时候，内部类都被作为成员内部类定义，而不是作为局部内部类。**成员内部类**是一种与成员变量、方法、构造器和初始化块相似的类成员；**局部内部类和匿名内部类则不是类成员**。
* 如果内部类成员变量、外部类成员变量与内部类里方法的局部变量同名，则可以通过使用this、外部类类名.this作为限定来区分
```java
public class DiscernVariable
{
	private String prop = "外部类的实例变量";
	private class InClass
	{
		private String prop = "内部类的实例变量";
		public void info()
		{
			var prop = "局部变量";
			// 通过 外部类类名.this.varName 访问外部类实例变量
			System.out.println("外部类的实例变量值："
				+ DiscernVariable.this.prop);
			// 通过 this.varName 访问内部类实例的变量
			System.out.println("内部类的实例变量值：" + this.prop);
			// 直接访问局部变量
			System.out.println("局部变量的值：" + prop);
		}
	}
	public void test()
	{
		var in = new InClass();
		in.info();
	}
	public static void main(String[] args)
	{
		new DiscernVariable().test();
	}
}
```

* 如果外部类需要**访问非静态内部类的实例成员**，则必须**显示创建非静态内部类对象来调用访问其实例成员**
* 不允许外部类的静态成员直接使用非静态内部类，不允许在非静态内部类里定义静态成员

* 静态内部类。使用static修饰一个内部类，则这个内部类就属于外部类本身，而不属于外部类的某个对象。外部类依然不能直接访问静态内部类的成员，但可以**使用静态内部类的类名作为调用者来访问静态内部类的类成员**，也可以**使用静态内部类对象作为调用者来访问静态内部类的实例成员**。
* 允许在接口定义内部类，默认使用public static修饰，也就是说，**接口内部类只能是静态内部类**
* 在外部类以外的地方使用内部类（没有private修饰），内部类完整的类名应该是**OuterClass.InnerClass**。如果外部类有包名，则还应该增加包名前缀。
* 在外部类以外的地方创建非静态内部类实例必须使用**外部类实例**和**new**来调用非静态内部类的构造器
```java
class Out
{
	// 定义一个内部类，不使用访问控制符，
	// 即只有同一个包中其他类可访问该内部类
	class In
	{
		public In(String msg)
		{
			System.out.println(msg);
		}
	}
}
public class CreateInnerInstance
{
	public static void main(String[] args)
	{
		Out.In in = new Out().new In("测试信息");
		/*
		上面代码可改为如下三行代码：
		使用OuterClass.InnerClass的形式定义内部类变量
		Out.In in;
		创建外部类实例，非静态内部类实例将寄存在该实例中
		Out out = new Out();
		通过外部类实例和new来调用内部类构造器创建非静态内部类实例
		in = out.new In("测试信息");
		*/
	}
}
当创建一个子类时，子类构造器总会调用父类的构造器，因此在创建非静态内部类的子类时，必须保证让子类构造器可以调用非静态内部类的构造器，【调用非静态内部类的构造器时，必须存在一个外部类对象】
public class SubClass extends Out.In
{
	//显示定义SubClass的构造器
	public SubClass(Out out)
	{
		//通过传入的Out外部类对象显式调用In的构造器
		out.super("hello");
	}
}
```

* 在外部类以外创建静态内部类实例**new <u>OuterClass.InnerConstructor()</u>**。不管是静态内部类还是非静态内部类，它们声明变量的语法完全一样。区别只是在创建内部类对象时，**静态内部类只需使用外部类即可调用构造器**，而**非静态内部类必须使用外部类对象来调用构造器**。相比之下，使用静态内部类比使用非静态内部类要简单很多，只要把外部类当成静态内部类的包空间即可。因此**当需要使用内部类时，优先考虑静态内部类**
* 内部类放在方法里定义，即局部内部类。不能使用访问控制符和static修饰符修饰。
* 编译后，Java为局部内部类的class文件名增加了一个数字用于区分 (因为所在方法不同，同一个类中可能有两个以上同名局部内部类)
* **匿名内部类**适合创建那种只需要一次使用的类，**创建匿名内部类时会立即创建一个该类的实例，这个类定义立即消失，匿名内部类不能重复使用**。
```java
new 实现接口 | 父类构造器(实参列表) {
	// 匿名内部类的类体部分
}
```

* 匿名内部类必须继承一个父类，或实现一个接口（最多只能继承一个父类，或实现一个接口）
  * 匿名内部类不能是抽象类（因为创建匿名内部类时会立即创建匿名内部类的对象）
  * 因为没有类名，所以不能定义构造器，但可以定义初始化块
  * **最常用的创建匿名内部类的方式是需要创建某个接口类型的对象** (只需要使用一次)
```java
interface Product
{
	double getPrice();
	String getName();
}
public class AnonymousTest
{
	public void test(Product p)
	{
		System.out.println("购买了一个" + p.getName()
			+ "，花掉了" + p.getPrice());
	}
	public static void main(String[] args)
	{
		var ta = new AnonymousTest();
		// 调用test()方法时，需要传入一个Product参数，
		// 此处传入其匿名实现类的实例
		ta.test(new Product()
		{
			public double getPrice()
			{
				return 567.8;
			}
			public String getName()
			{
				return "AGP显卡";
			}
		});
	}
}
如果这个Product接口实现类需要重复使用，则将该实现类定义成一个独立类；如果只需要使用一次，则采用上面形式
```

* 由于匿名内部类不能是抽象类，所以**匿名内部类必须实现它的抽象父类或者接口里包含的所有抽象方法**。通过接口来创建匿名内部类时，其内部不能显式地定义构造器，因此只有一个隐式的无参数构造器，故new接口名后的括号里不能传入参数值。但如果通过继承父类来创建匿名内部类时，其内部将拥有和父类相似的构造器，此处的相似指的是拥有相同的形参列表
```java
abstract class Device
{
	private String name;
	public abstract double getPrice();
	public Device(){}
	public Device(String name)
	{
		this.name = name;
	}
	public void setName(String name)
	{
		this.name = name;
	}
	public String getName()
	{
		return this.name;
	}
}
public class AnonymousInner
{
	public void test(Device d)
	{
		System.out.println("购买了一个" + d.getName()
			+ "，花掉了" + d.getPrice());
	}
	public static void main(String[] args)
	{
		var ai = new AnonymousInner();
		// 调用有参数的构造器创建Device匿名实现类的对象
		ai.test(new Device("电子示波器")
		{
			public double getPrice()
			{
				return 67.8;
			}
		});
		// 调用无参数的构造器创建Device匿名实现类的对象
		var d = new Device()
		{
			// 初始化块
			{
				System.out.println("匿名内部类的初始化块...");
			}
			// 实现抽象方法
			public double getPrice()
			{
				return 56.2;
			}
			// 重写父类的实例方法
			public String getName()
			{
				return "键盘";
			}
		};
		ai.test(d);
	}
}
```

* Java8之前要求被局部内部类、匿名内部类访问的局部变量必须使用final修饰，Java8开始这个限制被取消了：如果局部变量被匿名内部类访问，那么该局部变量相当于自动使用了final修饰。将这个功能称之为**“effective final”**

```java
interface A
{
	void test();
}
public class ATest
{
	public static void main(String[] args)
	{
		int age = 8;    
		// 下面代码将会导致编译错误
		// 由于age局部变量被匿名内部类访问了，因此age相当于被final修饰了
		// age = 2;
		var a = new A()  // 匿名内部类
		{
			public void test()
			{
				// 在Java 8以前下面语句将提示错误：age必须使用final修饰
				// 从Java 8开始，匿名内部类、局部内部类允许访问“非final”的局部变量
				System.out.println(age);
			}
		};
		a.test();
	}
}
```

* Lambda表达式是Java8的重要更新。其支持将代码块作为方法参数，允许使用更简洁的代码来创建**只有一个抽象方法的接口**（函数式接口）的实例。**Lambda表达式完全可用于简化创建匿名内部类对象**。
```java
public class CommandTest
{
	public static void main(String[] args)
	{
		var pa = new ProcessArray();
		int[] target = {3, -4, 6, 4};
		// 处理数组，具体处理行为取决于匿名内部类
		pa.process(target, new Command()
		{
			public void process(int element)
			{
				System.out.println("数组元素的平方是:" + element * element);
			}
		});
	}
}
用Lambda表达式改为如下形式
public class CommandTest2
{
	public static void main(String[] args)
	{
		var pa = new ProcessArray();
		int[] array = {3, -4, 6, 4};
		// 处理数组，具体处理行为取决于匿名内部类
		pa.process(array, (int element)->{
				System.out.println("数组元素的平方是:" + element * element);
			});
	}
}
```

* Lambda表达式由三部分组成：
  * 形参列表。允许省略形参类型，如果形参列表中只有一个参数，甚至连形参列表的圆括号可以省略
  * 箭头（->）
  * 代码块。如果只包含一条语句，则允许省略代码块的花括号。只有一条return语句，可以省略return关键字。Lambda表达式需要返回值，代码块中仅有一条省略了return的语句，则会直动返回这条语句的值
  ```java
  interface Eatable
  {
  	void taste();
  }
  interface Flyable
  {
  	void fly(String weather);
  }
  interface Addable
  {
  	int add(int a, int b);
  }
  public class LambdaQs
  {
  	// 调用该方法需要Eatable对象
  	public void eat(Eatable e)
  	{
  		System.out.println(e);
  		e.taste();
  	}
  	// 调用该方法需要Flyable对象
  	public void drive(Flyable f)
  	{
  		System.out.println("我正在驾驶：" + f);
  		f.fly("【碧空如洗的晴日】");
  	}
  	// 调用该方法需要Addable对象
  	public void test(Addable add)
  	{
  		System.out.println("5与3的和为：" + add.add(5, 3));
  	}
  	public static void main(String[] args)
  	{
  		var lq = new LambdaQs();
  		// Lambda表达式的代码块只有一条语句，可以省略花括号。
  		lq.eat(() -> System.out.println("苹果的味道不错！")); //相当于new Eatable() {...}
  		// Lambda表达式的形参列表只有一个形参，省略圆括号
  		lq.drive(weather -> {
  			System.out.println("今天天气是：" + weather);
  			System.out.println("直升机飞行平稳");
  		});
  		// Lambda表达式的代码块只有一条语句，省略花括号
  		// 代码块中只有一条语句，即使该表达式需要返回值，也可以省略return关键字。
  		lq.test((a, b) -> a + b);
  	}
  }
  LambdaQs$$Lambda$1/0x0000000100061040@2a098129
  苹果的味道不错！
  我正在驾驶：LambdaQs$$Lambda$2/0x0000000100067840@6956de9
  今天天气是：【碧空如洗的晴日】
  直升机飞行平稳
  5与3的和为：8
  ```

* Lambda表达式会被当做**一个“任意类型”的对象**，到底需要当成何种类型的对象，这取决于运行环境的需要

* Lambda表达式的**目标类型必须是“函数式接口“**，函数式接口可以包含多个默认方法、类方法，但只能**声明一个抽象方法**。Runnable、ActionListener等接口都是函数式接口

* 为了保证Lambda表达式的目标类型是一个明确的函数式接口，可以有如下三种常见方式：

  * 将Lambda表达式赋值给函数式接口类型的变量
  * 将Lambda表达式作为函数式接口类型的参数传给某个方法
  * 使用函数式接口对Lambda表达式进行强制类型转换
  ```java
  Object obj1 = (Runnable)() -> {
      for(var i = 0; i < 100; ++i) {
          System.out.println();
      }
  }
  ```

* Lambda表达式的目标类型完全可能是变化的——唯一的要求是，**Lambda表达式实现的匿名方法与目标类型（函数式接口）中唯一的抽象方法有相同的形参列表**
* Java8在java.util.function包下定义了大量函数式接口（见书本P221页）
* 如果Lambda表达式的代码块只有一条代码，程序就可以省略代码块的花括号。不仅如此，还可以在代码块中使用**方法引用**和**构造器引用**
  * 引用类方法：类名::类方法    等价于   (a,b,....) -> 类名.类方法(a,b,....)
  * 引用特定对象的实例方法：特定对象::实例方法    等价于   (a,b,....) -> 特定对象.实例方法(a,b,....)
  * 引用某类对象的实例方法：类名::实例方法    等价于   (a,b,....) -> a.实例方法(b,....)[第一个参数作为调用者]
  * 引用构造器： 类名::new     等价于     (a,b,....) -> new  类名(a,b,....)
```java
引用类方法
@FunctionalInterface  // 注解，本书第14章的内容
interface Converter{
	Integer convert(String from);
}
@FunctionalInterface
interface MyTest
{
	String test(String a, int b, int c);
}
@FunctionalInterface
interface YourTest
{
	JFrame win(String title);
}
public class MethodRefer
{
	public static void main(String[] args)
	{
		// 下面代码使用Lambda表达式创建Converter对象
		Converter converter1 = from -> Integer.valueOf(from);
		// 方法引用代替Lambda表达式：引用类方法。
		// 函数式接口中被实现方法的全部参数传给该类方法作为参数。
//		Converter converter1 = Integer::valueOf;
		Integer val = converter1.convert("99");
		System.out.println(val); // 输出整数99


		// 下面代码使用Lambda表达式创建Converter对象
		Converter converter2 = from -> "fkit.org".indexOf(from);
		// 方法引用代替Lambda表达式：引用特定对象的实例方法。
		// 函数式接口中被实现方法的全部参数传给该方法作为参数。
//		Converter converter2 = "fkit.org"::indexOf;
		Integer value = converter2.convert("it");
		System.out.println(value); // 输出2


		// 下面代码使用Lambda表达式创建MyTest对象
		MyTest mt = (a, b, c) -> a.substring(b, c);
		// 方法引用代替Lambda表达式：引用某类对象的实例方法。
		// 函数式接口中被实现方法的第一个参数作为调用者，
		// 后面的参数全部传给该方法作为参数。
//		MyTest mt = String::substring;
		String str = mt.test("Java I Love you", 2, 9);
		System.out.println(str); // 输出:va I Lo


		// 下面代码使用Lambda表达式创建YourTest对象
		YourTest yt = a -> new JFrame(a);
		// 构造器引用代替Lambda表达式。
		// 函数式接口中被实现方法的全部参数传给该构造器作为参数。
//		YourTest yt = JFrame::new;
		JFrame jf = yt.win("我的窗口");
		System.out.println(jf);
	}
}

```

* Lambda表达式和匿名内部类的联系和区别
  * 两者都可以直接访问“effectively final”的局部变量，以及外部类的成员变量（包括实例变量和类变量）
  * 两者**生成的对象都可以直接调用从接口中继承的默认方法**
  * 匿名内部类可以为任何接口创建实例；但**Lambda表达式只能为函数式接口创建实例**
  * 匿名内部类可以为抽象类甚至普通类创建实例；但**Lambda表达式只能为函数式接口创建实例**
  * 匿名内部类实现的抽象方法的方法体允许调用接口中定义的默认方法；但**Lambda表达式的代码块不允许调用接口中定义的默认方法**
* 使用Lambda表达式调用Arrays的类方法，Arrays类的有些方法需要Comparator、XxxOperator、XxxFunction等接口的实例，这些接口都是函数式接口。
```java
public class LambdaArrays
{
	public static void main(String[] args)
	{
		var arr1 = new String[] {"java", "fkava", "fkit", "ios", "android"};
		Arrays.parallelSort(arr1, (o1, o2) -> o1.length() - o2.length());
		System.out.println(Arrays.toString(arr1));
		var arr2 = new int[] {3, -4, 25, 16, 30, 18};
		// left代表数组中前一个所索引处的元素，计算第一个元素时，left为1
		// right代表数组中当前索引处的元素
		Arrays.parallelPrefix(arr2, (left, right)-> left * right);
		System.out.println(Arrays.toString(arr2));
		var arr3 = new long[5];
		// operand代表正在计算的元素索引
		Arrays.parallelSetAll(arr3, operand -> operand * 5);
		System.out.println(Arrays.toString(arr3));
	}
}
[ios, java, fkit, fkava, android]
[3, -12, -300, -4800, -144000, -2592000]
[0, 5, 10, 15, 20]
```

* **枚举类**：实例有限而且固定的类，**enum**关键字（和class、interface地位相同）。一个Java源文件中最多只能定义一个public访问权限的枚举类，且该Java源文件也必须和该枚举类的类名相同。与普通类区别如下
  * 枚举类可以实现一个或多个接口，使用enum定义的枚举类默认继承了java.lang.Enum类，而不是Object类，因此枚举类不能显示继承其他父类。其中java.lang.Enum类实现了java.lang.Serializable和java.lang.Comparable两个接口
  * 使用enum定义、**非抽象**的枚举类默认会使用final修饰
  * 枚举类的**构造器默认使用private修饰**，也只能使用private修饰。因此**枚举类不能派生子类**
  * 枚举类的所有实例必须在枚举类的第一行显示列出，否则这个枚举类永远都不能产生实例。列出这些实例时，系统会自动添加public static final修饰，无须显示添加
```java
文件SeasonEnum.java
public enum SeasonEnum
{
	// 在第一行列出4个枚举实例
	SPRING, SUMMER, FALL, WINTER;
}
文件EnumTest.java
public class EnumTest
{
	public void judge(SeasonEnum s)
	{
		// switch语句里的表达式可以是枚举值
		switch (s)
		{
			case SPRING:
				System.out.println("春暖花开，正好踏青");
				break;
			case SUMMER:
				System.out.println("夏日炎炎，适合游泳");
				break;
			case FALL:
				System.out.println("秋高气爽，进补及时");
				break;
			case WINTER:
				System.out.println("冬日雪飘，围炉赏雪");
				break;
		}
	}
	public static void main(String[] args)
	{
		// 枚举类默认有一个values方法，返回该枚举类的所有实例
		for (var s : SeasonEnum.values())
		{
			System.out.println(s);
			System.out.println(s.compareTo(SeasonEnum.SUMMER));
		}
		// 使用枚举实例时，可通过EnumClass.variable形式来访问
		new EnumTest().judge(SeasonEnum.SPRING);
	}
}
values()返回枚举类的所有实例。当switch控制表达式使用枚举类型时，后面case表达式中的值直接使用枚举值的名字，无须添加枚举类作为限定。java.lang.Enum类中提供了一些方法（见书本P228页）
```

* 枚举类一样可以定义成员变量、方法和构造器，通常应该设计成不可变类，因此建议将枚举类的成员变量都使用private final修饰。如果将所有的成员变量都使用了final修饰符，则必须在构造器里为这些成员变量指定初始值（在定义成员变量时或者在初始化块中指定初始值也可以，但不常见）。**一旦为枚举类显示定义了带参数的构造器，列出枚举值时就必须对应地传入参数**
```java
public enum Gender
{
	// 此处的枚举值(枚举实例)必须调用对应构造器来创建
	MALE("男"), FEMALE("女"); //相当于public static final Gender MALE = new Gender("男")
	private final String name;
	// 枚举类的构造器只能使用private修饰
	private Gender(String name)  // 为枚举类显示定义了带参数的构造器
	{
		this.name = name;
	}
	public String getName()
	{
		return this.name;
	}
}
```

* 如果由枚举类来实现接口里的方法，则每个枚举值在调用该方法时都有相同的行为方式（因为方法体完全一样）。**如果需要每个枚举值在调用该方法时呈现出不同的行为方式，则可以让每个枚举值分别来实现该方法**，每个枚举值提供不同的实现方法，从而让不同的枚举值调用该方法时具有不同的行为方式
```java
文件GenderDesc.java
public interface GenderDesc
{
	void info();
}
文件Gender.java
public enum Gender implements GenderDesc
{
	// 此处的枚举值必须调用对应构造器来创建
	MALE("男")
	// 花括号部分实际上是一个类体部分，类似于匿名内部类语法。当创建MALE枚举值时，并不是直接创建Gender枚举类的实例，而是相当于创建Gender的匿名子类的实例（MALE和FEMALE此处实际上是Gender匿名子类的实例）。所以此处Gender其实是抽象枚举类，默认使用abstract修饰
	{
		public void info()
		{
			System.out.println("这个枚举值代表男性");
		}
	},
	FEMALE("女")
	{
		public void info()
		{
			System.out.println("这个枚举值代表女性");
		}
	};
	private final String name;
	// 枚举类的构造器只能使用private修饰
	private Gender(String name)
	{
		this.name = name;
	}
	public String getName()
	{
		return this.name;
	}
下面是通用info()版本，枚举值在调用info时具有相同的行方式
//	// 此处的枚举值必须调用对应构造器来创建
//	MALE("男"), FEMALE("女");
//	// 增加下面的info()方法，实现GenderDesc接口必须实现的方法
//	public void info()
//	{
//		System.out.println(
//			"这是一个用于用于定义性别的枚举类");
//	}
}
```

* 编译下面的程序生成5个class文件，其中Operation对应一个class文件，它的4个匿名内部子类分别对应一个
```java
public enum Operation
{
	PLUS
	{
		public double eval(double x, double y)
		{
			return x + y;
		}
	},
	MINUS
	{
		public double eval(double x, double y)
		{
			return x - y;
		}
	},
	TIMES
	{
		public double eval(double x, double y)
		{
			return x * y;
		}
	},
	DIVIDE
	{
		public double eval(double x, double y)
		{
			return x / y;
		}
	};
	// 为枚举类定义一个抽象方法
	// 这个抽象方法由不同的枚举值提供不同的实现
	public abstract double eval(double x, double y);
	public static void main(String[] args)
	{
		System.out.println(Operation.PLUS.eval(3, 4));
		System.out.println(Operation.MINUS.eval(5, 4));
		System.out.println(Operation.TIMES.eval(5, 4));
		System.out.println(Operation.DIVIDE.eval(5, 4));
	}
}
```

* 枚举类里定义抽象方法时**不能使用abstract关键字将枚举类定义成抽象类**（系统自动添加），但因为枚举类需要显示创建枚举值，而不是作为父类，所以**定义每个枚举值时必须为抽象方法提供实现，否则编译错误**
* Java的垃圾回收机制具有如下特征：
  * 垃圾回收机制只负责回收堆内存的对象，不会回收任何物理资源
  * 程序无法精确控制垃圾回收的运行，垃圾回收会在合适的时候进行
  * 在垃圾回收机制回收任何对象之前，总会先调用它的finalize()方法，该方法可能使该对象重新复活
* 对象在内存中的状态：
  * 可达状态：当一个对象被创建后，有一个以上的引用变量引用它，程序可通过引用变量来调用该对象
  * 可恢复状态：某个对象不再有任何引用变量引用它。
  * 不可达状态：当对象与所有引用变量的关联都被切断，且系统已经调用所有对象的finalize()方法后依然没有使该对象变成可达状态，那么这个对象将永久性地失去引用，最后变成不可达状态。只有当一个对象处于不可达状态时，系统才会真正回收该对象所占有的资源

* 可以强制系统进行垃圾回收——这种强制只是通知系统进行垃圾回收，但系统是否进行依然不确定：
  * 调用System类的gc()静态方法：System.gc()
  * 调用Runtime对象的gc()实例方法：Runtime.getRuntime().gc()
* 在垃圾回收机制回收某个对象所占用的内存之前，通常要求程序调用适当的方法来清理资源，在没有明确指定清理资源的情况下，Java提供了默认机制来清理该对象的资源，这个机制就是**finalize()**方法。该方法是定义在Object类里的实例方法，方法原型为：protected void finalize() throws Throwable
* 任何Java类都可以重写Object类的finalize()方法，在该方法中清理该对象占用的资源。垃圾回收机制何时调用对象的finalize()方法是完全透明的，只有当程序认为需要更多的额外内存时，垃圾回收机制才会进行垃圾回收。finalize()方法具有如下几个特点：
  * 永远不要主动调用某个对象的finalize()方法，该方法应交给垃圾回收机制调用
  * finalize()方法何时被调用，是否被调用具有不确定性，不要把它当成一定会被执行的方法
  * 当JVM执行可恢复对象的finalize()方法时，可能使该对象或系统中的其他对象重新变成可达状态
  * 当JVM执行finalize()方法时出现异常，垃圾回收机制不会报告异常，程序继续执行
```java
public class FinalizeTest
{
	private static FinalizeTest ft = null;
	public void info()
	{
		System.out.println("测试资源清理的finalize方法");
	}
	public static void main(String[] args) throws Exception
	{
		// 创建FinalizeTest对象（匿名对象）立即进入可恢复状态
		new FinalizeTest();
		// 通知系统进行资源回收
		System.gc();  // ①
		// 强制垃圾回收机制调用可恢复对象的finalize()方法
		// Runtime.getRuntime().runFinalization();   // ②
		System.runFinalization();   // ③
		ft.info();
	}
	public void finalize()
	{
		// 让ft引用到试图回收的可恢复对象，即可恢复对象重新变成可达
		ft = this;
	}
}
如果取消①号代码，程序没有通知系统开始执行垃圾回收，系统通常不会立即进行垃圾回收，也就不会调用FinalizeTest对象的finalize()方法，这样FinalizeTest的ft类变量将依然保持为null，这样就导致了空指针异常。
如果仅执行①号代码，而不执行②号或③号代码，由于垃圾回收机制的不确定性，JVM往往并不立即调用可恢复对象的finalize方法，依然会导致空指针异常
```

* java.lang.ref包下提供了三个类：SoftReference、WeakReference和PhantomReference，即对象的软、弱和虚引用
  * **强引用**：程序创建一个对象，并把这个对象赋给一个引用变量，程序通过该变量来操作实际的对象
  * **软引用**：SoftReference类来实现。对一个对象只有软引用时，它有可能被垃圾回收机制回收。当系统内存足够，它不会被回收，程序也可使用该对象；当系统内存空间不足时，系统可能会回收它。
  * **弱引用**：WeakReference来实现。对于只有弱引用的对象而言，当系统垃圾回收机制运行时，不管系统内存是否足够，总会回收该对象所占用的内存
  * **虚引用**：PhantomReference类来实现，虚引用完全类似于没有引用。如果一个对象只有一个虚引用时，那么它和没有引用的效果大致相同。虚引用主要用于跟踪对象被垃圾回收的状态，**虚引用不能单独使用，虚引用必须和引用队列（ReferenceQueue）联合使用**。
  * 三个引用类（不包括虚引用）都包含了一个get()方法，用于获取被它们所引用的对象
  * 如果以软、弱和虚引用的方法引用对象，垃圾回收器就能够随意地释放对象。如果希望尽可能减小程序在其生命周期中所占用的内存大小时，这些引用类就很有用处。注意，要使用这些特殊引用类，就不能保留对对象的强引用；如果保留了对对象的强引用，就会浪费这些引用类所提供的任何好处
* **引用队列**（ReferenceQueue）由java.lang.refReferenceQueue类表示，它用于保存被回收后对象的引用。软、弱引用和引用队列联合使用时，系统在回收被引用的对象之后，将把被回收对象对应的引用添加到关联的引用队列中。与软、弱引用不同的是，虚引用在对象被释放之前，将把它对应的虚引用添加到它关联的引用队列中，这使得可以在对象被回收之前采取行动。虚引用只能和引用队列联合使用
```java
文件ReferenceTest.java
public class ReferenceTest
{
	public static void main(String[] args)
		throws Exception
	{
		// 创建一个字符串对象
		var str = new String("疯狂Java讲义");
		// 创建一个弱引用，让此弱引用引用到"疯狂Java讲义"字符串
		var wr = new WeakReference(str); 
		// 切断str引用和"疯狂Java讲义"字符串之间的引用
		str = null;   
		// 取出弱引用所引用的对象
		System.out.println(wr.get());  // 输出“疯狂Java讲义”
		// 强制垃圾回收
		System.gc();
		System.runFinalization();
		// 再次取出弱引用所引用的对象
		System.out.println(wr.get());  // 输出null
	}
}
文件PhantomReferenceTest.java
public class PhantomReferenceTest
{
	public static void main(String[] args)
		throws Exception
	{
		// 创建一个字符串对象
		var str = new String("疯狂Java讲义");
		// 创建一个引用队列
		var rq = new ReferenceQueue();
		// 创建一个虚引用，让此虚引用引用到"疯狂Java讲义"字符串
		var pr = new PhantomReference(str, rq);
		// 切断str引用和"疯狂Java讲义"字符串之间的引用
		str = null;
		// 取出虚引用所引用的对象，并不能通过虚引用获取被引用的对象，所以此处输出null
		System.out.println(pr.get()); 
		// 强制垃圾回收
		System.gc();
		System.runFinalization();
		// 垃圾回收之后，虚引用将被放入引用队列中
		// 取出引用队列中最先进入队列中的引用与pr进行比较
		System.out.println(rq.poll() == pr);   // true
	}
}
```

* 由于垃圾回收的不确定性，当程序希望从软、弱引用中取出被引用对象时，可能这个被引用对象已经被释放了。如果程序需要使用那个被引用的对象，则必须重新创建该对象。有两种方式实现：
```java
注意，下面两段都是伪代码
方式1:
// 取出弱引用所引用的对象
obj = wr.get();
// 如果取出的对象为null
if(obj == null) {
    // 重新创建一个新的对象，再次让弱引用去引用该对象
    wr = new WeakReference(recreateIt());
    obj = wr.get();
}
...// 操作obj对象   该方式较方式2有缺陷，因为if块即使被执行，由于垃圾回收的不确定，obj可能依然为null
// 再次切断obj与对象之间的关联
obj = null;

方式2
// 取出弱引用所引用的对象
obj = wr.get();
// 如果取出的对象为null
if(obj == null) {
    // 重新创建一个新的对象，并使用强引用来引用它
    obj = recreateIt();
    // 创建弱引用
    wr = new WeakReference(obj);
}
...// 操作obj对象      方式2，当if块执行后，obj一定不为null
// 再次切断obj与对象之间的关联
obj = null;
```

* 关于**修饰符使用范围**的一些总结，以及**jar文件相关的一些命名**，请见书本P240