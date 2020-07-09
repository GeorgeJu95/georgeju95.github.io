---
title: Java面向对象：接口与多态
date: 2020-02-24 15:36:36
categories: 
toc: true
tags: [Java]
---

## 接口、抽象类

```java
package com.itheima_01;
/*
 * 
 * 类与类：继承关系，单一继承，多层继承
 * 类与接口：实现关系，多实现
 * 接口与接口的关系：继承关系，多继承
 */
public class InterfaceDemo3 {
	public static void main(String[] args) {
		
	}
}

interface InterA extends InterB {
	public abstract void method();
}

interface InterB {
	public abstract void function();
}

interface InterC extends InterA {
	
}

class Demo implements InterC {

	@Override
	public void method() {
		// TODO Auto-generated method stub
	}

	@Override
	public void function() {
		// TODO Auto-generated method stub
	}	
}
```

<!-- more -->

* 接口的优点
  
  * 打破了Java单一继承的局限性
  * 对外提供规则（USB接口）
  * 降低了程序的耦合性 （可以实现模块化开发，定义好规则，每个人实现自己的模块，提高了开发的效率）
  
* 接口和抽象类的区别
  * 共性：不断的抽取，抽取出抽象的概念
  * 区别1：与类的关系
    * 类与接口是实现关系，而且是多实现，一个类可以实现多个接口
    * 类与抽象类是继承关系，Java中的继承是单一继承、多层继承，一个类只能有一个父类
  * 区别2：成员
    * 成员变量：抽象类可以有成员变量，也可以有常量，但是接口只能有常量
    * 成员方法：抽象类可以有抽象方法，也可以有非抽象方法。接口只能有抽象方法，而且方法有默认修饰符 public abstract
    * 构造方法：抽象类有构造方法，接口没有构造方法

* 运动员案例分析

  ![design](/assets/blogImg/design.png)

  ```java
  package com.itheima_02;
  /* 	
   * 	篮球运动员和教练
  	乒乓球运动员和教练
  	现在篮球运动员和教练要出国访问,需要学习英语
  	请根据你所学的知识,分析出来哪些是类,哪些是抽象类,哪些是接口
   */
  public class InterfaceTest {
  	public static void main(String[] args) {
  		//创建篮球运动员对象
  		BasketBallPlayer bbp = new BasketBallPlayer();
  		bbp.name = "女兆月日";
  		bbp.age = 35;
  		bbp.gender = "男";
  		bbp.sleep();
  		bbp.study();
  		bbp.speak();
  		System.out.println("-------------");
  		//创建乒乓球教练对象
  		PingpangCoach ppc = new PingpangCoach();
  		ppc.name = "刘胖子";
  		ppc.age = 40;
  		ppc.gender = "男";
  		ppc.sleep();
  		ppc.teach();
  		//ppc.speak();
  	}
  }
  
  class Person {
  	String name;//姓名
  	int age;//年龄
  	String gender;//性别
  	
  	//无参构造
  	public Person() {}
  	
  	//有参构造
  	public Person(String name,int age,String gender) {
  		this.name = name;
  		this.age = age;
  		this.gender = gender;
  	}
  	
  	//吃
  	public void eat() {
  		System.out.println("吃饭");
  	}
  	
  	//睡
  	public void sleep() {
  		System.out.println("睡觉");
  	}
  }
  
  //学习说英语
  interface SpeakEnglish {
  	public abstract void speak();
  }
  
  //运动员
  abstract class Player extends Person {
  	//学习
  	public abstract void study();
  }
  
  //教练
  abstract class Coach  extends Person {
  	//教
  	public abstract void teach();
  }
  
  //篮球运动员
  class BasketBallPlayer extends Player  implements SpeakEnglish{
  
  	@Override
  	public void study() {
  		System.out.println("学扣篮");
  	}
  
  	@Override
  	public void speak() {
  		System.out.println("说英语");
  	}
  	
  }
  //乒乓球运动员
  class PingpangPlayer extends Player {
  
  	@Override
  	public void study() {
  		System.out.println("学抽球");
  	}
  	
  }
  //篮球教练
  class BasketBallCoach extends Coach implements SpeakEnglish {
  
  	@Override
  	public void teach() {
  		System.out.println("教扣篮");
  	}
  
  	@Override
  	public void speak() {
  		System.out.println("说英语");
  	}
  	
  }
  //乒乓球教练
  class PingpangCoach extends Coach {
  
  	@Override
  	public void teach() {
  		System.out.println("教抽球");
  	}	
  }
  ```

## 匿名对象

* 没有名字的对象
* 匿名对象的应用场景：
  * 当方法只调用一次时可以使用匿名对象
  * 可以当作参数进行传递，但是无法在传参之前做其他的事情 

## final关键字

```java
package com.itheima_01;
/*
 * final： 修饰符，可以用于修饰类、成员方法和成员变量
 * final所修饰的类：不能被继承，不能有子类
 * final所修饰的方法:不能被重写
 * final所修饰的变量：是不可以修改的，是常量
 * 
 * 常量：
 * 		字面值常量：1,2,3
 * 		自定义常量：被final所修饰的成员变量，一旦初始化则不可改变
 * 
 * 注意：自定义常量必须初始化，可以选择显示初始化或者构造初始化
 * 
 *  
 */
public class FinalDemo {
	public static void main(String[] args) {
		//Animal a = new Animal();
		//a.eat();
		
		Dog d = new Dog();
		//d.eat();
		
		//d.num = 20;
		System.out.println(d.NUM);
	}
}

/*final*/ class Animal {
	public final void eat() {
		System.out.println("吃东西"); 
	}
}

class Dog extends Animal {
	/*public void eat() {}*/
	
	final int NUM;
	
	public Dog() {
		NUM = 10;
	}
}
```

## 多态

 ```java
package com.itheima_01;
/*
 * 多态的前提：
 * 	子父类的继承关系
 * 	方法的重写
 * 	父类引用指向子类对象
 * 
 * 动态绑定：运行期间调用的方法，是根据其具体的类型
 */
public class PoymorphicDemo {
	public static void main(String[] args) {
		/*Cat c = new Cat();
		c.eat();*/
		
		//父类引用 Animal a
		//指向	 =
		//子类对象 new Cat()
		
		Animal a = new Cat();
		a.eat();
		
	}
}

class Animal {
	public void eat() {
		System.out.println("吃东西");
	}
}

class Cat extends Animal {
	public void eat() {
		System.out.println("猫吃鱼");
	}
}
 ```

```java
package com.itheima_01;
/*
 *	
 * 	多态的成员特点:
 * 		成员变量  编译时看的是左边，运行时看的左边
 * 		成员方法  编译时看的是左边，运行时看右边
 * 		静态方法  编译时看的是左边，运行时看的也是左边
 * 
 * 编译时看的都是左边，运行时成员方法看的是右边，其他（成员变量和静态的方法）看的都是左边
 */
public class PoymorphicDemo2 {
	public static void main(String[] args) {
		Dad d = new Kid();
		//System.out.println(d.num);
		
		//d.method();
		d.function();//使用变量去调用静态方法，其实相当于用变量类型的类名去调用
	}
}

class Dad {
	int num = 20;
	
	public void method() {
		System.out.println("我是父类方法");
	}
	
	public static void function() {
		System.out.println("我是父类静态方法");
	}
}

class Kid extends Dad {
	int num = 10;
	
	public void method() {
		System.out.println("我是子类方法");
	}
	
	public static void function() {
		System.out.println("我是子类静态方法");
	}
}
```

```java
package com.itheima_01;
/*	
 * 	多态中的向上转型和向下转型:
 * 
 *  引用类型之间的转换
 *  	向上转型
 *  		由小到大(子类型转换成父类型)
 *  	向下转型
 *  		由大到小
 *  基本数据类型的转换
 *  	自动类型转换
 *  		由小到大
 *  		byte short char --- int --- long --- float --- double
 *  	强制类型转换
 *  		由大到小
 */
public class PoymorphicDemo3 {
	public static void main(String[] args) {
		Animal2 a = new Dog();//向上转型
		//a.eat();
		
		Dog d = (Dog)a;//向下转型
		d.swim();
		
	}
}

class Animal2 {
	public void eat() {
		System.out.println("吃东西");
	}
}

class Dog extends Animal2 {
	public void eat() {
		System.out.println("啃骨头");
	}
	
	public void swim() {
		System.out.println("狗刨");
	}
}
```

* 多态的优缺点

  ```java
  package com.itheima_01;
  /*	
   * 	多态的优缺点
   * 		优点：可以提高可维护性（多态前提所保证的），提高代码的可扩展性
   *	  缺点：无法直接访问子类特有的成员
   */
  public class PoymorphicDemo4 {
  	public static void main(String[] args) {
  		MiFactory factory = new MiFactory();
  		factory.createPhone(new MiNote());
  		
  		factory.createPhone(new RedMi());
  	}
  }
  
  class MiFactory {
  	/*public void createPhone(MiNote mi) {
  		mi.call();
  	}
  	
  	public void createPhone(RedMi mi) {
  		mi.call();
  	}*/
  	
  	public void createPhone(Phone p) {
  		p.call();
  	}	
  }
  
  interface Phone {
  	public void call();
  }
  
  //小米Note
  class MiNote implements Phone{
  	public void call() {
  		System.out.println("小米Note打电话");
  	}
  }
  
  //红米
  class RedMi implements Phone {
  	public void call() {
  		System.out.println("红米打电话");
  	}
  }
  ```

  