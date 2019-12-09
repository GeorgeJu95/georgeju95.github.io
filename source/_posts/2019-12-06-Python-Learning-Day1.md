---
title: 字符串、列表、元组、集合、字典
date: 2019-12-06 18:17:36
categories: 
toc: true
tags: [Python]
---

## 字符串

* 复数类型，不是常用数据类型

  ```Python
  c = 1 + 5j
  print(type(c)) # <class 'complex'>
  ```

* 身份运算符

  ```python
  flag1 = 3 > 2
  flag2 = 2 < 1
  print(flag1 is True) # True
  print(flag2 is not False) # False
  ```
  <!-- more -->

* 模块化管理函数

  * Python**没有函数重载的概念**，那么后面的定义会覆盖之前的定义，也就意味着两个函数同名函数实际上只有一个是存在的。

  * **在不同的模块中可以有同名的函数**，在使用函数的时候我们通过`import`关键字导入指定的模块就可以区分到底要使用的是哪个模块中的函数

    ```python
    【module1.py】
    def foo():
        print('hello, world!')
    【module2.py】
    def foo():
        print('goodbye, world!')
    【test.py】
    import module1 as m1
    import module2 as m2
    m1.foo()
    m2.foo()
    ```

* \_\_name__是Python中一个隐含的变量它代表了模块的名字，**只有被Python解释器直接执行的模块的名字才是\_\_main\_\_**

  ```python
  【module3.py】
  def foo():
      pass
  
  def bar():
      pass
      
  # __name__是Python中一个隐含的变量它代表了模块的名字
  # 只有被Python解释器直接执行的模块的名字才是__main__
  if __name__ == '__main__':
      print('call foo()')
      foo()
      print('call bar()')
      bar()
  【test.py】
  import module3
  # 导入module3时 不会执行模块中if条件成立时的代码 因为模块的名字是module3而不是__main_
  # 把上面那串代码放到test执行模块中才正确，即
  # if __name__ == '__main__':
  #     print('call foo()')
  #     module3.foo()
  #     print('call bar()')
  #     module3.bar()
  ```

* 如果不希望字符串中的`\`表示转义，在字符串的最前面加上字母`r`来加以说明

  ```python
  s1 = '\'hello, world!\''
  s2 = '\n\\hello, world!\\\n'
  print(s1, s2, end='')  
  # 'hello, world!' 
  # \hello, world!\
  
  s1 = r'\'hello, world!\''
  s2 = r'\n\\hello, world!\\\n'
  print(s1, s2, end='')
  # \'hello, world!\' \n\\hello, world!\\\n
  ```

* Python为字符串类型提供了非常丰富的运算符，我们可以使用`+`运算符来实现字符串的拼接，可以使用`*`运算符来重复一个字符串的内容，可以使用`in`和`not in`来判断一个字符串是否包含另外一个字符串（成员运算），我们也可以用`[]`和`[:]`运算符从字符串取出某个字符或某些字符（切片运算），代码如下所示

  ```python
  s1 = 'hello ' * 3
  print(s1) # hello hello hello 
  s2 = 'world'
  s1 += s2
  print(s1) # hello hello hello world
  print('ll' in s1) # True
  print('good' in s1) # False
  str2 = 'abc123456'
  # 从字符串中取出指定位置的字符(下标运算)
  print(str2[2]) # c
  # 字符串切片(从指定的开始索引到指定的结束索引)
  print(str2[2:5]) # c12
  print(str2[2:]) # c123456
  print(str2[2::2]) # c246
  print(str2[::2]) # ac246
  print(str2[::-1]) # 654321cba
  print(str2[-3:-1]) # 45
  ```

* 在Python中，我们还可以通过一系列的方法来完成对字符串的处理

  ```python
  str1 = 'hello, world!'
  # 通过内置函数len计算字符串的长度
  print(len(str1)) # 13
  # 获得字符串首字母大写的拷贝
  print(str1.capitalize()) # Hello, world!
  # 获得字符串每个单词首字母大写的拷贝
  print(str1.title()) # Hello, World!
  # 获得字符串变大写后的拷贝
  print(str1.upper()) # HELLO, WORLD!
  # 从字符串中查找子串所在位置
  print(str1.find('or')) # 8
  print(str1.find('shit')) # -1
  # 与find类似但找不到子串时会引发异常
  # print(str1.index('or'))
  # print(str1.index('shit'))
  # 检查字符串是否以指定的字符串开头
  print(str1.startswith('He')) # False
  print(str1.startswith('hel')) # True
  # 检查字符串是否以指定的字符串结尾
  print(str1.endswith('!')) # True
  # 将字符串以指定的宽度居中并在两侧填充指定的字符
  print(str1.center(50, '*'))
  # 将字符串以指定的宽度靠右放置左侧填充指定的字符
  print(str1.rjust(50, ' '))
  str2 = 'abc123456'
  # 检查字符串是否由数字构成
  print(str2.isdigit())  # False
  # 检查字符串是否以字母构成
  print(str2.isalpha())  # False
  # 检查字符串是否以数字和字母构成
  print(str2.isalnum())  # True
  str3 = '  jackfrued@126.com '
  print(str3)
  # 获得字符串修剪左右两侧空格之后的拷贝
  print(str3.strip())
  ```

## 列表

* 向列表中添加和删除元素

  ```python
  list1 = [1, 3, 5, 7, 100]
  # 添加元素
  list1.append(200)
  list1.insert(1, 400)
  # 合并两个列表
  # list1.extend([1000, 2000])
  list1 += [1000, 2000]
  print(list1) # [1, 400, 3, 5, 7, 100, 200, 1000, 2000]
  print(len(list1)) # 9
  # 先通过成员运算判断元素是否在列表中，如果存在就删除该元素
  if 3 in list1:
  	list1.remove(3)
  if 1234 in list1:
      list1.remove(1234)
  print(list1) # [1, 400, 5, 7, 100, 200, 1000, 2000]
  # 从指定的位置删除元素
  list1.pop(0)
  list1.pop(len(list1) - 1)
  print(list1) # [400, 5, 7, 100, 200, 1000]
  # 清空列表元素
  list1.clear()
  print(list1) # []
  ```

* 对列表的排序操作

  ```python
  list1 = ['orange', 'apple', 'zoo', 'internationalization', 'blueberry']
  list2 = sorted(list1)
  # sorted函数返回列表排序后的拷贝不会修改传入的列表
  # 函数的设计就应该像sorted函数一样尽可能不产生副作用
  list3 = sorted(list1, reverse=True)
  # 通过key关键字参数指定根据字符串长度进行排序而不是默认的字母表顺序
  list4 = sorted(list1, key=len)
  print(list1)
  print(list2)
  print(list3)
  print(list4)
  # 给列表对象发出排序消息直接在列表对象上进行排序
  list1.sort(reverse=True)
  print(list1)
  
  ['orange', 'apple', 'zoo', 'internationalization', 'blueberry']
  ['apple', 'blueberry', 'internationalization', 'orange', 'zoo']
  ['zoo', 'orange', 'internationalization', 'blueberry', 'apple']
  ['zoo', 'apple', 'orange', 'blueberry', 'internationalization']
  ['zoo', 'orange', 'internationalization', 'blueberry', 'apple']
  ```

## 生成式和生成器语法

* 生成式语法**

  ```python
  import sys
  
  f = [x for x in range(1, 10)]
  print(f)
  f = [x + y for x in 'ABCDE' for y in '1234567']
  print(f)
  # 用列表的生成表达式语法创建列表容器
  # 用这种语法创建列表之后元素已经准备就绪所以需要耗费较多的内存空间
  f = [x ** 2 for x in range(1, 1000)]
  print(sys.getsizeof(f))  # 查看对象占用内存的字节数,4508
  print(f)
  # 请注意下面的代码创建的不是一个列表而是一个生成器对象
  # 通过生成器可以获取到数据但它不占用额外的空间存储数据
  # 每次需要数据的时候就通过内部的运算得到数据(需要花费额外的时间)
  f = (x ** 2 for x in range(1, 1000))
  print(sys.getsizeof(f))  # 相比生成式生成器不占用存储数据的空间,56
  print(f)
  for val in f:
      print(val)
  ```

* 除了上面提到的生成器语法，Python中还有另外一种定义生成器的方式，就是通过`yield`关键字将一个普通函数改造成生成器函数。下面的代码演示了如何实现一个生成[斐波拉切数列](https://zh.wikipedia.org/wiki/斐波那契数列)的生成器

  ```python
  def fib(n):
      a, b = 0, 1
      for _ in range(n):
          a, b = b, a + b
          yield a
  
  def main():
      for val in fib(20):
          print(val)
  
  if __name__ == '__main__':
      main()
  ```

## 元组

* Python中的元组与列表类似也是一种容器数据类型，可以用一个变量（对象）来存储多个数据，**不同之处在于元组的元素不能修改**。顾名思义，我们把多个元素组合到一起就形成了一个元组，所以它和列表一样可以保存多条数据

  ```python
  # 定义元组
  t = ('骆昊', 38, True, '四川成都')
  print(t)
  # 获取元组中的元素
  print(t[0])
  print(t[3])
  # 遍历元组中的值
  for member in t:
      print(member)
  # 重新给元组赋值
  # t[0] = '王大锤'  # TypeError
  # 变量t重新引用了新的元组原来的元组将被垃圾回收
  t = ('王大锤', 20, True, '云南昆明')
  print(t)
  # 将元组转换成列表
  person = list(t)
  print(person)
  # 列表是可以修改它的元素的
  person[0] = '李小龙'
  person[1] = 25
  print(person)
  # 将列表转换成元组
  fruits_list = ['apple', 'banana', 'orange']
  fruits_tuple = tuple(fruits_list)
  print(fruits_tuple)
  ```

* 有一个非常值得探讨的问题，**我们已经有了列表这种数据结构，为什么还需要元组这样的类型呢**？

  * **元组中的元素是无法修改的**，事实上我们在项目中尤其是[多线程](https://zh.wikipedia.org/zh-hans/多线程)环境（后面会讲到）中可能更喜欢使用的是那些不变对象（一方面因为对象状态不能修改，所以可以避免由此引起的不必要的程序错误，简单的说就是一个不变的对象要比可变的对象更加容易维护；另一方面因为没有任何一个线程能够修改不变对象的内部状态，一个不变对象自动就是线程安全的，这样就可以省掉处理同步化的开销。一个不变对象可以方便的被共享访问）。所以结论就是：如果不需要对元素进行添加、删除、修改的时候，可以考虑使用元组，当然如果一个方法要返回多个值，使用元组也是不错的选择。

  * **元组在创建时间和占用的空间上面都优于列表**。我们可以使用sys模块的getsizeof函数来检查存储同样的元素的元组和列表各自占用了多少内存空间，这个很容易做到。我们也可以在ipython中使用魔法指令%timeit来分析创建同样内容的元组和列表所花费的时间，下图是我的Windows系统上测试的结果。

    ```python
    In [1]: %timeit [1,2,3,4,5]
    57.1 ns ± 1.54 ns per loop (mean ± std. dev. of 7 runs, 10000000 loops each)
    
    In [2]: %timeit (1,2,3,4,5)
    8.82 ns ± 0.146 ns per loop (mean ± std. dev. of 7 runs, 100000000 loops each)
    ```

## 集合

* 集合，和数学上的集合一致，也有交集、并集、差集等运算

  ```python
  # 创建集合的字面量语法
  set1 = {1, 2, 3, 3, 3, 2}
  print(set1)
  print('Length =', len(set1))
  # 创建集合的构造器语法(面向对象部分会进行详细讲解)
  set2 = set(range(1, 10))
  set3 = set((1, 2, 3, 3, 2, 1))
  print(set2, set3)
  # 创建集合的推导式语法(推导式也可以用于推导集合)
  set4 = {num for num in range(1, 100) if num % 3 == 0 or num % 5 == 0}
  print(set4)
  
  # {1, 2, 3}
  # Length = 3
  # {1, 2, 3, 4, 5, 6, 7, 8, 9} {1, 2, 3}
  # {3, 5, 6, 9, 10, 12, 15, 18, 20, 21, 24, 25, 27, 30, 33, 35, 36, 39, 40, 42, 45, 48, 50, 51, 54, 55, 57, 60, 63, 65, 66, 69, 70, 72, 75, 78, 80, 81, 84, 85, 87, 90, 93, 95, 96, 99}
  
  set1.add(4)
  set1.add(5)
  set2.update([11, 12])
  set2.discard(5)
  if 4 in set2:
      set2.remove(4)
  print(set1, set2)
  print(set3.pop())
  print(set3)
  
  # {1, 2, 3, 4, 5} {1, 2, 3, 6, 7, 8, 9, 11, 12}
  # 1
  # {2, 3}
  
  # 集合的交集、并集、差集、对称差运算
  print(set1 & set2)
  # print(set1.intersection(set2))
  print(set1 | set2)
  # print(set1.union(set2))
  print(set1 - set2)
  # print(set1.difference(set2))
  print(set1 ^ set2)
  # print(set1.symmetric_difference(set2))
  # 判断子集和超集
  print(set2 <= set1)
  # print(set2.issubset(set1))
  print(set3 <= set1)
  # print(set3.issubset(set1))
  print(set1 >= set2)
  # print(set1.issuperset(set2))
  print(set1 >= set3)
  # print(set1.issuperset(set3))
  
  # {1, 2, 3}
  # {1, 2, 3, 4, 5, 6, 7, 8, 9, 11, 12}
  # {4, 5}
  # {4, 5, 6, 7, 8, 9, 11, 12}
  # False
  # True
  # False
  # True
  ```

## 字典

* 字典是另一种可变容器模型，Python中的字典跟我们生活中使用的字典是一样一样的，它可以存储任意类型对象，与列表、集合不同的是，**字典的每个元素都是由一个键和一个值组成的**“键值对”，键和值通过冒号分开。下面的代码演示了如何定义和使用字典。

  ```python
  # 创建字典的字面量语法
  scores = {'骆昊': 95, '白元芳': 78, '狄仁杰': 82}
  print(scores)
  # 创建字典的构造器语法
  items1 = dict(one=1, two=2, three=3, four=4)
  # 通过zip函数将两个序列压成字典
  items2 = dict(zip(['a', 'b', 'c'], '123'))
  # 创建字典的推导式语法
  items3 = {num: num ** 2 for num in range(1, 10)}
  print(items1, items2, items3)
  # 通过键可以获取字典中对应的值
  print(scores['骆昊'])
  print(scores['狄仁杰'])
  # 对字典中所有键值对进行遍历
  for key in scores:
      print(f'{key}: {scores[key]}')
  # 更新字典中的元素
  scores['白元芳'] = 65
  scores['诸葛王朗'] = 71
  scores.update(冷面=67, 方启鹤=85)
  print(scores)
  if '武则天' in scores:
      print(scores['武则天'])
  print(scores.get('武则天'))
  # get方法也是通过键获取对应的值但是可以设置默认值
  print(scores.get('武则天', 60))
  # 删除字典中的元素
  print(scores.popitem())
  print(scores.popitem())
  print(scores.pop('骆昊', 100))
  # 清空字典
  scores.clear()
  print(scores)
  
  # {'骆昊': 95, '白元芳': 78, '狄仁杰': 82}
  # {'one': 1, 'two': 2, 'three': 3, 'four': 4} {'a': '1', 'b': '2', 'c': '3'} # {1: 1, 2: 4, 3: 9, 4: 16, 5: 25, 6: 36, 7: 49, 8: 64, 9: 81}
  # 95
  # 82
  # 骆昊: 95
  # 白元芳: 78
  # 狄仁杰: 82
  # {'骆昊': 95, '白元芳': 65, '狄仁杰': 82, '诸葛王朗': 71, '冷面': 67, '方启鹤': 85}
  # None
  # 60
  # ('方启鹤', 85)
  # ('冷面', 67)
  # 95
  # {}
  ```

  