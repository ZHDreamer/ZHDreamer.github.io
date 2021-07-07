# Python 第四次上机作业
## 栈的类
>编写代码，实现一个栈（Stack）的类。栈是只能在一端插入和删除数据的序列。它按照先进后出的原则存储数据，先进入的数据被压入栈底，最后的数据在栈顶，需要读数据的时候从栈顶开始弹出数据（最后一个数据被第一个读出来）。
```python
class Stack:
    # 定义栈类
    def __init__(self):
        self.items = []
    # 初始化建立一个列表，用于模拟栈
    def __str__(self):
        return str(self.items)
    # 指定print格式
    def push(self, item):
        self.items.append(item)
    # push入栈，在列表尾部增加一个元素
    def pop(self):
        return self.items.pop()
    # pop出栈，将列表尾部pop掉
    def top(self):
        return self.items[-1]
    # 利用负索引查看栈顶
    def size(self):
        return len(self.items)
    # 返回列表长度
    def clear(self):
        self.items = []
    # 清空列表


stack = Stack()
stack.push(1)
stack.push(2)
print(stack)
print("pop:", stack.pop())
print(stack)
stack.push(3)
print("top:", stack.top())
print("size:", stack.size())
stack.clear()
print("clear:", stack)
```
### \_\_new\_\_() 和 \_\_init\_\_() 的区别
- __new__是在实例创建之前被调用的，因为它的任务就是创建实例然后返回该实例对象，是个静态方法，但是无须使用@staticmethod 修饰。
- __init__是当实例对象创建完成后被调用的，然后设置对象属性的一些初始值，通常用在初始化一个类实例的时候。是一个实例方法。
  __new__先被调用，__init__后被调用，__new__的返回值（实例）将传递给__init__方法的第一个参数，然后__init__给这个实例设置一些参数。
1. 继承自object的新式类才有__new__
2. __new__至少要有一个参数cls，代表当前类，此参数在实例化时由Python解释器自动识别
3. __new__必须要有返回值，返回实例化出来的实例，这点在自己实现__new__时要特别注意，可以return父类（通过super(当前类名, cls)）__new__出来的实例，或者直接是object的__new__出来的实例
4. __init__有一个参数self，就是这个__new__返回的实例，__init__在__new__的基础上可以完成一些其它初始化的动作，__init__不需要返回值
### 实例方法，类方法和静态方法
实例方法无须额外修饰，第一个参数是self，绑定调用此方法的实例对象。
类方法需要@classmethod修饰，第一个参数是cls，绑定现在的类。
静态方法需要@staticmethod 修饰，没有特殊参数。
一般不用类方法和静态方法，因为可以直接用外部函数实现。
#### 方法调用
调用方法可以用类名也可以用对象名。
- 调用实例方法一般用对象名，若用类名调用，需要传入对象名作为self参数
- 调用类方法和静态方法两种方法都可以
### \_\_str\_\_() 的作用
当使用print输出对象的时候，只要自己定义了__str__(self)方法，那么就会打印从在这个方法中return的数据
## 形状类
>编写代码，定义一个形状基类，有 2 个属性：面积和周长，以及两个无返回值的方法：area()和 perimeter()，分别计算形状的面积和周长，从基类派生出三个子类：三角形、矩形、圆，重载基类的两个方法。 
### 形状基类
```python
import math


class Shape:
    def __init__(self, *args):
        self.sides = []
        if args:
            for arg in args:
                self.sides.append(arg)
        self.Area()
        self.Perimeter()

    def Area(self):
        self.area = 0

    def Perimeter(self):
        self.perimeter = 0
```
### 三角形类
```python
class Triangle(Shape):
    # 重载Area和Perimeter方法
    def Area(self):
        if len(self.sides) == 3:
            S = (
                math.sqrt(
                    (self.sides[0] + self.sides[1] + self.sides[2])
                    * (self.sides[0] + self.sides[1] - self.sides[2])
                    * (self.sides[0] - self.sides[1] + self.sides[2])
                    * (-self.sides[0] + self.sides[1] + self.sides[2])
                )
                / 4
            )
            self.area = S
        else:
            self.area = 0

    def Perimeter(self):
        if len(self.sides) != 3:
            self.perimeter = 0
        else:
            C = 0
            for side in self.sides:
                C += side
            self.perimeter = C
```
### 矩形类
```python
class Rectangle(Shape):
    def Area(self):
        if len(self.sides) == 2:
            S = self.sides[0] * self.sides[1]
            self.area = S
        else:
            self.area = 0

    def Perimeter(self):
        if len(self.sides) != 2:
            self.perimeter = 0
        else:
            C = 0
            for side in self.sides:
                C += side
            self.perimeter = C * 2
```
### 圆类
```python
class Circle(Shape):
    def Area(self):
        if len(self.sides) == 1:
            S = math.pi * self.sides[0] ** 2
            self.area = S
        else:
            self.area = 0

    def Perimeter(self):
        if len(self.sides) == 1:
            C = math.pi * self.sides[0] * 2
            self.perimeter = C
        else:
            self.area = 0
```
### 测试代码
```python
tri = Triangle(3, 4, 5)
print(tri.area)
print(tri.perimeter)

rect = Rectangle(5, 4)
print(rect.area)
print(rect.perimeter)

circle = Circle(10)
print(circle.area)
print(circle.perimeter)
```
### 输出
```
6.0
12
20
18
314.1592653589793
62.83185307179586
```