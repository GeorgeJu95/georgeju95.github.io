---
title: Python——面向对象基础
date: 2019-12-30 18:17:36
categories: 
toc: true
tags: [Python]
---

## 访问可见性问题——封装

* 在Class内部，可以有属性和方法，而外部代码可以通过直接调用实例变量的方法来操作数据，这样，就隐藏了内部的复杂逻辑。如果要让内部属性不被外部访问，可以把属性的名称前加上两个下划线`__`，在Python中，实例的变量名如果以`__`开头，就变成了一个私有变量（private），只有内部可以访问，外部不能访问。

* 需要注意的是，在Python中，变量名类似`__xxx__`的，也就是以双下划线开头，并且以双下划线结尾的，是特殊变量，特殊变量是可以直接访问的，不是private变量，所以，不能用`__name__`、`__score__`这样的变量名。

* 有些时候，你会看到以一个下划线开头的实例变量名，比如`_name`，这样的实例变量外部是可以访问的，但是，按照约定俗成的规定，当你看到这样的变量时，意思就是，“虽然我可以被访问，但是，请把我视为私有变量，不要随意访问”。

* 双下划线开头的实例变量是不是一定不能从外部访问呢？其实也不是。不能直接访问`__name`是因为Python解释器对外把`__name`变量改成了`_Student__name`，所以，仍然可以通过`_Student__name`来访问`__name`变量。但是强烈建议你不要这么干，因为不同版本的Python解释器可能会把`__name`改成不同的变量名。总的来说就是，Python本身没有任何机制阻止你干坏事，一切全靠自觉。

  <!-- more -->
  
  ```python
  class Test:
  
      def __init__(self, foo):
          self.__foo = foo
  
      def __bar(self):
          print(self.__foo)
          print('__bar')
  
  
  def main():
      test = Test('hello')
      # AttributeError: 'Test' object has no attribute '__bar'
      test.__bar()
      # AttributeError: 'Test' object has no attribute '__foo'
      print(test.__foo)
  
  
  if __name__ == "__main__":
    main()
  ```

  下面的是可以顺利执行的
  
  ```python
  class Test:
  
      def __init__(self, foo):
          self.__foo = foo
  
      def __bar(self):
          print(self.__foo)
          print('__bar')
  
  
  def main():
      test = Test('hello')
      test._Test__bar()
      print(test._Test__foo)
  
  
  if __name__ == "__main__":
      main()
  ```

## 参考内容

1. https://www.liaoxuefeng.com/wiki/1016959663602400/1017496679217440
2. https://github.com/jackfrued/Python-100-Days/blob/master/Day01-15/08.面向对象编程基础.md
