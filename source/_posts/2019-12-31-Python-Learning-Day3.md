---
title: Python——面向对象进阶
date: 2019-12-31 16:17:36
categories: 
toc: true
tags: [Python]
---

### @property装饰器

将属性命名以单下划线开头，通过这种方式来暗示属性是受保护的，不建议外界直接访问，那么如果想访问属性可以通过属性的getter（访问器）和setter（修改器）方法进行对应的操作。如果要做到这点，就可以考虑使用@property包装器来包装getter和setter方法，使得对属性的访问既安全又方便，代码如下所示。

<!-- more -->

```Python
class Person(object):

    def __init__(self, name, age):
        self._name = name
        self._age = age

    # 访问器 - getter方法
    @property
    def name(self):
        return self._name

    # 访问器 - getter方法
    @property
    def age(self):
        return self._age

    # 修改器 - setter方法
    @age.setter
    def age(self, age):
        self._age = age

    def play(self):
        if self._age <= 16:
            print('%s正在玩飞行棋.' % self._name)
        else:
            print('%s正在玩斗地主.' % self._name)

def main():
    person = Person('王大锤', 12)
    person.play()
    person.age = 22
    person.play()
    # person.name = '白元芳'  # AttributeError: can't set attribute


if __name__ == '__main__':
    main()
```

### \_\_slots\_\_魔法

如果我们需要限定自定义类型的对象只能绑定某些属性，可以通过在类中定义__slots__变量来进行限定。需要注意的是__slots__的限定只对当前类的对象生效，对子类并不起任何作用。

```Python
class Person(object):

    # 限定Person对象只能绑定_name, _age和_gender属性
    __slots__ = ('_name', '_age', '_gender')

    def __init__(self, name, age):
        self._name = name
        self._age = age

    @property
    def name(self):
        return self._name

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, age):
        self._age = age

    def play(self):
        if self._age <= 16:
            print('%s正在玩飞行棋.' % self._name)
        else:
            print('%s正在玩斗地主.' % self._name)

def main():
    person = Person('王大锤', 22)
    person.play()
    person._gender = '男'
    # AttributeError: 'Person' object has no attribute '_is_gay'
    # person._is_gay = True
```

### 静态方法和类方法

* 静态方法属于类而不是对象

  ```Python
  from math import sqrt
  
  class Triangle(object):
      def __init__(self, a, b, c):
          self._a = a
          self._b = b
          self._c = c
  
      @staticmethod
      def is_valid(a, b, c):
          return a + b > c and b + c > a and a + c > b
  
      def perimeter(self):
          return self._a + self._b + self._c
  
      def area(self):
          half = self.perimeter() / 2
          # 海伦公式
          return sqrt(half * (half - self._a) *
                      (half - self._b) * (half - self._c))
  
  def main():
      a, b, c = 3, 4, 5
      # 静态方法和类方法都是通过给类发消息来调用的
      if Triangle.is_valid(a, b, c):
          t = Triangle(a, b, c)
          print(t.perimeter())
          # 也可以通过给类发消息来调用对象方法但是要传入接收消息的对象作为参数
          # print(Triangle.perimeter(t))
          print(t.area())
          # print(Triangle.area(t))
      else:
          print('无法构成三角形.')
  
  if __name__ == '__main__':
      main()
  ```

* 类方法，类方法的第一个参数约定名为cls，它代表的是当前类相关的信息的对象（类本身也是一个对象，有的地方也称之为类的元数据对象），通过这个参数我们可以获取和类相关的信息并且可以创建出类的对象。

  ```python
  from time import time, localtime, sleep
  
  class Clock(object):
      """数字时钟"""
  
      def __init__(self, hour=0, minute=0, second=0):
          self._hour = hour
          self._minute = minute
          self._second = second
  
      @classmethod
      def now(cls):
          ctime = localtime(time())
          return cls(ctime.tm_hour, ctime.tm_min, ctime.tm_sec)
  
      def run(self):
          """走字"""
          self._second += 1
          if self._second == 60:
              self._second = 0
              self._minute += 1
              if self._minute == 60:
                  self._minute = 0
                  self._hour += 1
                  if self._hour == 24:
                      self._hour = 0
  
      def show(self):
          """显示时间"""
          return '%02d:%02d:%02d' % \
                 (self._hour, self._minute, self._second)
  
  def main():
      # 通过类方法创建对象并获取系统时间
      clock = Clock.now()
      while True:
          print(clock.show())
          sleep(1)
          clock.run()
  
  if __name__ == '__main__':
      main()
  ```

## 多态

子类在继承了父类的方法后，可以对父类已有的方法给出新的实现版本，这个动作称之为方法重写（override）。通过方法重写我们可以让父类的同一个行为在子类中拥有不同的实现版本，当我们调用这个经过子类重写的方法时，不同的子类对象会表现出不同的行为，这个就是多态（poly-morphism）。

```python
from abc import ABCMeta, abstractmethod

class Pet(object, metaclass=ABCMeta):
    """宠物"""

    def __init__(self, nickname):
        self._nickname = nickname

    @abstractmethod
    def make_voice(self):
        """发出声音"""
        pass

class Dog(Pet):
    """狗"""

    def make_voice(self):
        print('%s: 汪汪汪...' % self._nickname)

class Cat(Pet):
    """猫"""

    def make_voice(self):
        print('%s: 喵...喵...' % self._nickname)

def main():
    pets = [Dog('旺财'), Cat('凯蒂'), Dog('大黄')]
    for pet in pets:
        pet.make_voice()

if __name__ == '__main__':
    main()
```

在上面的代码中，我们将`Pet`类处理成了一个抽象类，所谓抽象类就是不能够创建对象的类，这种类的存在就是专门为了让其他类去继承它。Python从语法层面并没有像Java或C#那样提供对抽象类的支持，但是我们可以通过`abc`模块的`ABCMeta`元类和`abstractmethod`包装器来达到抽象类的效果，如果一个类中存在抽象方法那么这个类就不能够实例化（创建对象）。上面的代码中，`Dog`和`Cat`两个子类分别对`Pet`类中的`make_voice`抽象方法进行了重写并给出了不同的实现版本，当我们在`main`函数中调用该方法时，这个方法就表现出了多态行为（同样的方法做了不同的事情）。

## \_\_str\_\_、\_\_repr\_\_

参考下面第三条链接

```Python
import random

class Card(object):
    """一张牌"""

    def __init__(self, suite, face):
        self._suite = suite
        self._face = face

    @property
    def face(self):
        return self._face

    @property
    def suite(self):
        return self._suite

    def __str__(self):
        if self._face == 1:
            face_str = 'A'
        elif self._face == 11:
            face_str = 'J'
        elif self._face == 12:
            face_str = 'Q'
        elif self._face == 13:
            face_str = 'K'
        else:
            face_str = str(self._face)
        return '%s%s' % (self._suite, face_str)
    
    def __repr__(self):
        return self.__str__()

class Poker(object):
    """一副牌"""

    def __init__(self):
        self._cards = [Card(suite, face) 
                       for suite in '♠♥♣♦'
                       for face in range(1, 14)]
        self._current = 0

    @property
    def cards(self):
        return self._cards

    def shuffle(self):
        """洗牌(随机乱序)"""
        self._current = 0
        random.shuffle(self._cards)

    @property
    def next(self):
        """发牌"""
        card = self._cards[self._current]
        self._current += 1
        return card

    @property
    def has_next(self):
        """还有没有牌"""
        return self._current < len(self._cards)

class Player(object):
    """玩家"""

    def __init__(self, name):
        self._name = name
        self._cards_on_hand = []

    @property
    def name(self):
        return self._name

    @property
    def cards_on_hand(self):
        return self._cards_on_hand

    def get(self, card):
        """摸牌"""
        self._cards_on_hand.append(card)

    def arrange(self, card_key):
        """玩家整理手上的牌"""
        self._cards_on_hand.sort(key=card_key)

# 排序规则-先根据花色再根据点数排序
def get_key(card):
    return (card.suite, card.face)

def main():
    p = Poker()
    p.shuffle()
    players = [Player('东邪'), Player('西毒'), Player('南帝'), Player('北丐')]
    for _ in range(13):
        for player in players:
            player.get(p.next)
    for player in players:
        print(player.name + ':', end=' ')
        player.arrange(get_key)
        print(player.cards_on_hand)

if __name__ == '__main__':
    main()
```



## 参考内容

1. [理解Python装饰器(Decorator)](https://www.jianshu.com/p/ee82b941772a)
2. [面向对象进阶](https://github.com/jackfrued/Python-100-Days/blob/master/Day01-15/09.面向对象进阶.md)
3. [Python的两个魔法方法：\_\_repr\_\_和\_\_str\_\_](https://blog.csdn.net/sinat_41104353/article/details/79254149)