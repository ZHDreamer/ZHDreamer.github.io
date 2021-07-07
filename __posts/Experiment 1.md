# Python 第一次上机实验报告

## 一 实现四则运算并调用math库函数
通过 randint 函数生成随机数并对其进行基础运算
```python
import math
from random import randint

x, y = randint(1, 10), randint(-10, 10)
print(x, y)
print(x + y)
print(x - y)
print(x / y)
print(x // y)
print(x % y)
print(x ** y)
print(abs(y))
print(divmod(x, y))
print(math.log(x))
print(math.log10(x))
print(math.sqrt(x))
print(math.exp(x))
print(math.sin(x))
print(math.cos(x))
print(math.tan(x))
```
## 二 生成随机数并统计各个数字出现次数
>编写程序，生成包含 1000 个 0 到 100 之间的随机整数，并统计每个元素的出现次数。
```python
import random  # 引入随机数生成库

# 创建一个含有1000个0到100之间的随机整数
# randint包含上界
randlist = [random.randint(0, 100) for i in range(0, 1000)]  
count = [0] * 101  # 计数列表初始化为0，比每次list.append()有效率
for i in randlist:
    count[i] += 1  # 将计数值储存在对应数的下标中，方便访问
print(count)
```
## 三 列表排序
>编写程序，生成包含 20 个随机数的列表，然后将前 10 个元素升序排列，后 10 个元素降序排列，并输出结果。
```python
import random

alist = [random.randint(0, 100) for i in range(0, 20)]  
# 生成含有20个随机数的列表
print(alist)
# 利用切片分别对于列表前10个和后10个元素分别排序
frontlist = sorted(alist[:10])
latterlist = sorted(alist[10:], reverse=True)
sortedlist = frontlist + latterlist
print(sortedlist)  # 输出结果

```
### 为什么使用 sorted 而不是 sort
sorted和sort的区别就是是否改变原列表，sort 会将原列表排序，无返回值；而 sorted 会返回一个新的列表。
切片会对列表进行浅复制，因此用 sort 排序切片对原列表没有影响，会导致无法得到排序后的列表。
用sorted则可以获得一个新的排序好的列表，再对两个排序列表进行拼接

## 四 闰年判断
>编写程序，运行后用户输入 4 位整数作为年份，判断其是否为闰年。如果年份能被400 整除，则为闰年；如果年份能被 4 整除但不能被 100 整除也为闰年。
```python
def isleapyear(year):  # 定义一个判断闰年的函数
    if year % 400 == 0:  # 判断能不能被400整除
        return 1
    elif year % 4 == 0:  # 若能被4整除，判断其能不能被100整除
        if year % 100 == 0:
            return 0  # 能被整除，不是闰年
        else:
            return 1  # 不能被整除，是闰年
    return 0


year = int(input("please input a year\n"))
print(isleapyear(year))
```
### if 条件的顺序
判断能不能被400整除要放在能不能被4和100整除之前。
因为能被400整除的年份也能被4和100整除，按照后者判断应该不是闰年，但是能被400整除的是闰年，因此被400整除这个条件是要放在4和100整除之前。