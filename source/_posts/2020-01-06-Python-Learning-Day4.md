---
title: Python——正则表达式
date: 2020-01-10 16:17:36
categories: 
toc: true
tags: [Python]
---

## 文件读取与写入

* #### final 和 with 关键字

`try`后面跟上了三个`except`分别处理这三种不同的异常状况。最后我们使用`finally`代码块来关闭打开的文件，释放掉程序中获取的外部资源，由于`finally`块的代码不论程序正常还是异常都会执行到（甚至是调用了`sys`模块的`exit`函数退出Python环境，`finally`块都会被执行，因为`exit`函数实质上是引发了`SystemExit`异常），因此我们通常把`finally`块称为“总是执行代码块”，它最适合用来做释放外部资源的操作。如果不愿意在`finally`代码块中关闭文件对象释放资源，也可以使用上下文语法，通过`with`关键字指定文件对象的上下文环境并在离开上下文环境时自动释放文件资源。

<!-- more -->

```Python
def main():
    f = None
    try:
        f = open('致橡树.txt', 'r', encoding='utf-8')
        print(f.read())
    except FileNotFoundError:
        print('无法打开指定的文件!')
    except LookupError:
        print('指定了未知的编码!')
    except UnicodeDecodeError:
        print('读取文件时解码错误!')
    finally:
        if f:
            f.close()

if __name__ == '__main__':
    main()
```

```python
def main():
    try:
        with open('致橡树.txt', 'r', encoding='utf-8') as f:
            print(f.read())
    except FileNotFoundError:
        print('无法打开指定的文件!')
    except LookupError:
        print('指定了未知的编码!')
    except UnicodeDecodeError:
        print('读取文件时解码错误!')


if __name__ == '__main__':
    main()
```

```Python
import time

def main():
    # 一次性读取整个文件内容
    with open('致橡树.txt', 'r', encoding='utf-8') as f:
        print(f.read())

    # 通过for-in循环逐行读取
    with open('致橡树.txt', mode='r', encoding='utf-8') as f:
        for line in f:
            print(line, end='')
            time.sleep(0.5)
    print()

    # 读取文件按行读取到列表中
    with open('致橡树.txt', encoding='utf-8') as f:
        lines = f.readlines()
    print(lines)
    
if __name__ == '__main__':
    main()
    
【最后一个的输出】
['我如果爱你——\n', '绝不像攀援的凌霄花，\n', '借你的高枝炫耀自己；\n', '我如果爱你——\n', '绝不学痴情的鸟儿，\n', '为绿荫重复单调的歌曲；\n', '也不止像泉源，\n', '常年送来清凉的慰藉；\n', '也不止像险峰，\n', '增加你的高度，衬托你的威仪。\n', '甚至日光，\n', '甚至春雨。\n', '\n', '不，这些都还不够！\n', '我必须是你近旁的一株木棉，\n', '作为树的形象和你站在一起。\n', '根，紧握在地下；\n', '叶，相触在云里。\n', '每一阵风过，\n', '我们都互相致意，\n', '但没有人，\n', '听懂我们的言语。\n', '你有你的铜枝铁干，\n', '像刀，像剑，也像戟；\n', '我有我红硕的花朵，\n', '像沉重的叹息，\n', '又像英勇的火炬。\n', '\n', '我们分担寒潮、风雷、霹雳；\n', '我们共享雾霭、流岚、虹霓。\n', '仿佛永远分离，\n', '却又终身相依。\n', '这才是伟大的爱情，\n', '坚贞就在这里：\n', '爱——\n', '不仅爱你伟岸的身躯，\n', '也爱你坚持的位置，\n', '足下的土地。']
```

* #### 写入

```PYthon
from math import sqrt

def is_prime(n):
    """判断素数的函数"""
    assert n > 0
    for factor in range(2, int(sqrt(n)) + 1):
        if n % factor == 0:
            return False
    return True if n != 1 else False


def main():
    filenames = ('a.txt', 'b.txt', 'c.txt')
    fs_list = []
    try:
        for filename in filenames:
            fs_list.append(open(filename, 'w', encoding='utf-8'))
        for number in range(1, 10000):
            if is_prime(number):
                if number < 100:
                    fs_list[0].write(str(number) + '\n')
                elif number < 1000:
                    fs_list[1].write(str(number) + '\n')
                else:
                    fs_list[2].write(str(number) + '\n')
    except IOError as ex:
        print(ex)
        print('写文件时发生错误!')
    finally:
        for fs in fs_list:
            fs.close()
    print('操作完成!')


if __name__ == '__main__':
    main()
```

* #### JSON 序列化、反序列化

json模块主要有四个比较重要的函数，分别是：

- `dump` - 将Python对象按照JSON格式序列化到文件中
- `dumps` - 将Python对象处理成JSON格式的字符串
- `load` - 将文件中的JSON数据反序列化成对象
- `loads` - 将字符串的内容反序列化成Python对象

这里出现了两个概念，一个叫序列化，一个叫反序列化。自由的百科全书[维基百科](https://zh.wikipedia.org/)上对这两个概念是这样解释的：“序列化（serialization）在计算机科学的数据处理中，是指将数据结构或对象状态转换为可以存储或传输的形式，这样在需要的时候能够恢复到原先的状态，而且通过序列化的数据重新获取字节时，可以利用这些字节来产生原始对象的副本（拷贝）。与这个过程相反的动作，即从一系列字节中提取数据结构的操作，就是反序列化（deserialization）”。

下面的例子演示了如何使用 [requests](http://docs.python-requests.org/zh_CN/latest/) 模块（封装得足够好的第三方网络访问模块）访问网络API获取国内新闻，如何通过json模块解析JSON数据并显示新闻标题，这个例子使用了[天行数据](https://www.tianapi.com/)提供的国内新闻数据接口，其中的APIKey需要自己到该网站申请。

```python
import requests
import json


def main():
    resp = requests.get('http://api.tianapi.com/guonei/index?key=APIKey&num=10')
    data_model = json.loads(resp.text)
    for news in data_model['newslist']:
        print(news['title'])

if __name__ == '__main__':
    main()

# 输出
我驻纽约总领馆:请美东地区中国公民加强安全防范
第36届中国哈尔滨国际冰雪节开幕(组图)
香港公务员涉嫌轮番袭击他人自首后被捕 邮政署表态
多地探索设特别合作区：深汕首创 南京欲“扩张”
韩国瑜：我宁愿戴假发也不会穿防弹背心
港警“滥捕示威者”？香港终于要清理“毒教材”
华为技术有限公司注册资本新增1亿人民币
82岁著名数学家四川师范大学教授丁协平逝世
“绿委”叫嚣支持统一就是“叛国”后 他一通狠骂
国家统计局：今年全面实施地区生产总值统一核算
```

## 参考内容

1. [总结：Python中的异常处理](https://segmentfault.com/a/1190000007736783)

2. [Python-100-days：文件与异常](https://github.com/jackfrued/Python-100-Days/blob/master/Day01-15/11.%E6%96%87%E4%BB%B6%E5%92%8C%E5%BC%82%E5%B8%B8.md)