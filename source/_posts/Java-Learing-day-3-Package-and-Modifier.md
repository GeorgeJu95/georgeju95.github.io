---
title: Java面向对象：包&修饰符
date: 2020-02-24 15:36:36
categories: 
toc: true
tags: [Java]
---

## 包

* 包的特点：
  * 可以有多层
  * 不同包下的文件名可以重复
  * 包的声明必须是第一行代码

* 不同包之间的互相访问
  * 使用类的全名：包名+类名
  * 使用关键字 import 将类导入

<!-- more -->

## 修饰符

| public    | 当前类 | 相同包下不同的类 | 不同包下的类 |
| --------- | ------ | ---------------- | ------------ |
| default   | 当前类 | 相同包下不同的类 |              |
| private   | 当前类 |                  |              |
| protected | 当前类 | 相同包下不同的类 |              |

* default:     **当前包**下使用
  protected: 让**子类对象**使用 

| 修饰符    | 类   | 成员变量 | 成员方法 | 构造方法 |
| --------- | ---- | -------- | -------- | -------- |
| public    | Y    | Y        | Y        | Y        |
| default   | Y    | Y        | Y        | Y        |
| protected |      | Y        | Y        | Y        |
| private   |      | Y        | Y        | Y        |
| abstract  | Y    |          | Y        |          |
| static    |      | Y        | Y        |          |
| final     | Y    | Y        | Y        |          |

常见规则（开发中一般够用）：

* 使用 public 来修饰类，一个java文件中只能有一个类（实际开发默认），如果一个文件中有多个类，那么类名和文件名一样的类，必须使用public修饰，其他的类不能使用public修饰
* 所有的成员变量使用 private 修饰，对外提供setter和getter方法
* 所有的方法使用public修饰，除非有其他要求
* 所有的构造方法使用public修饰，即构造函数是公有的，其他任何地方都可以实例化该类。如果使用private修饰，就是私有构造函数，这个一般很少用，只有单例模式才会这样写，因为这样写了这个类就不能用普通方法实例化，只有用类里面的其他公有方法来调用构造函数实例化该类。

## 内部类

* 成员内部类：在类的成员位置，和成员变量、成员方法在同一个位置 

* 局部内部类：在方法内，和局部变量在同一个位置，出了方法之后就无法使用

* 匿名内部类：可以把匿名内部类看成是一个**没有名字**的局部内部类，定义在方法中。必须在定义匿名内部类的时候创建它的对象。格式：

  ```java
  new 类/接口() {
    如果是创建了继承这个类的子类对象，我们可以重写父类的方法
    如果是创建了实现这个接口的子类对象，我们必须要实现该接口的所有方法
  };
  
  原理：创建了继承这个类的子类对象，或是创建了实现这个接口的子类对象 
  ```

* 共性：

  * 内部类仍然是一个独立的类，在编译之后内部类会被编译成独立的`.class`文件，但是前面冠以外部类的类名和`$`符号
  * 内部类不能用普通的方式访问。内部类是外部类的一个成员，因此**内部类可以自由地访问外部类的成员变量，无论是否是private的**。

* 成员内部类代码

```java
package com.itheima_01;
/*
 * 成员内部类：
 * 		在类的成员位置，和成员变量以及成员方法所在的位置是一样的
 * 		在内部类当中，可以直接访问外部类的成员，包括私有成员
 */
public class InnerDemo {
	public static void main(String[] args) {
		//Outer o = new Outer();
		//o.method();
    
		Outer.Inner i = new Outer().new Inner();
		i.function();		
	}
}

class Outer {
	private int num = 10;
	
	public void method() {
		Inner i = new Inner();
		i.function();
	}
	
	class Inner {
		public void function() {
			System.out.println(num);
		}
	}
}
```

```java
package com.itheima_01;
/*
 * 成员内部类的修饰符：
 * 		我们可以使用权限修饰符修饰成员内部类，但是如果使用私有来修饰，则无法在其他类中访问
 * 		我们可以使用static修饰成员内部类，不用再创建外部类的对象了
 * 
 * 我们可以使用abstract,final修饰成员内部类，但没多大意义 
 */
public class InnerDemo2 {
	public static void main(String[] args) {
		//Outer2.Inner2 i;
		
		//Outer2.Inner2 i = new Outer2.Inner2();
		//i.function();
		
		Outer2.Inner2.function();
	}
}

class Outer2 {
	public void method() {
		Inner2 i = new Inner2();
	}
	
	static class Inner2 {
		public static void function() {
			System.out.println("function");
		}
	}
}
```

* 匿名内部类代码

```java
package com.itheima_03;
/*
 * 匿名内部类:
 * 		可以把匿名内部类看成是一个没有名字的局部内部类
 * 		定义在方法当中
 * 		必须在定义匿名内部类的时候创建他的对象
 * 格式：
 * 		new 类/接口(){
 * 			如果是创建了继承这个类的子类对象，我们可以重写父类的方法
 * 			如果是创建了实现这个接口的子类对象，我们必须要实现该接口的所有方法
 * 		};
 * 原理：而是创建了继承这个类的子类对象或者是创建了实现这个接口的子类对象
 * 	
 */
public class InnerDemo4 {
	public static void main(String[] args) {
		Outer o = new Outer();
		o.method();
	}
}

interface Inner {
	public void function();
}

class Outer {
	public void method() {
		
		/*new Inner() {

			@Override
			public void function() {
				System.out.println("function");
			}
			
		}.function();;*/
		
		Inner i = new Inner() {

			@Override
			public void function() {
				System.out.println("function");
			}
			
		};
		
		i.function();
		i.function();
	}
}
```

* 匿名内部类的应用场景
  * 作为参数进行传递（一般用在一个类对象只传递一次的情景，这样就不用去创建一个类文件了）

```java
[Animal.java]
package com.itheima_04;

public interface Animal {
	public abstract void eat();
}


[InnerDemo5.java]
package com.itheima_04;
/*
 * 匿名内部类的应用场景:
 * 		作为参数进行传递
 * 		
 *      
 */
public class InnerDemo5 {
	public static void main(String[] args) {
     //使用下面的方法调用，需要创建Dog类和Cat类
		 //method(new Dog());
		 //method(new Cat());
		
		method(
				new Animal() {

					@Override
					public void eat() {
						System.out.println("猫吃鱼");
					}
					
				}	
		);
	}	
	
	public static void method(Animal a) {
		a.eat();
	}
}
```

