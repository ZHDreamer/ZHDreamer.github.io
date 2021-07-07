# Python 第三次上机报告
## 1. 统计字符串不同字符个数
>编写函数，接收一个字符串，分别统计大写字母、小写字母、数字、其他字符的个数，并以元组的形式返回结果。
### 用字符串常量
```python
import string # 需要用到字符串常量


def count1(src):
    upper, lower, digital, punctuation = 0, 0, 0, 0
    # 分别记录大写字母，小写字母，数字和符号的数量
    for ch in src:
        # 用in判断字符类别
        if ch in string.ascii_uppercase:
            upper += 1
        elif ch in string.ascii_lowercase:
            lower += 1
        elif ch in string.digits:
            digital += 1
        else:
            punctuation += 1
    return (upper, lower, digital, punctuation)
    # 用元组返回


print(count1('a2Cd,sdfh839244￥&%*@0hllaeLHOIDFHso'))
```
### 用正则表达式
```python
import re #导入正则表达式模块


def count2(src):
    upper, lower, digital, punctuation = 0, 0, 0, 0
    for ch in src:
        # 利用 re.match() 以正则表达式的方式匹配
        if re.match('[a-z]', ch):
            upper += 1
        elif re.match('[A-Z]', ch):
            lower += 1
        elif re.match('\d', ch):
            digital += 1
        else:
            punctuation += 1
    return (upper, lower, digital, punctuation)


print(count2('a2Cd,sdfh839244￥&%*@0hllaeLHOIDFHso'))
```
用正则表达式可以更灵活的匹配不同要求的字符，比如要求m之前的字母分一类，m之后的分一类，利用正则表达式就可以很方便的进行匹配。

## 2. 求最大值和整数和
>编写函数，可以接收任意多个整数并输出其中的最大值和所有整数之和。
```python
import random
# 用于生成数列

def max_sum(*args):
    # *args为约定俗成的可变长形参
    # arg本身为元组
    if not args:
        return 'error: no input'
    # 因为可变长形参长度可以为0，不加这一句下面args[0]就会报错
    max = args[0] # 将args的第一个参数赋值给max，当遇到比max大的数就替换max
    sum = 0 # 先初始化sum值
    for num in args: # 遍历args
        if not isinstance(num, int): # 如果传入的并非整数，返回错误信息
            return 'error: only support instance of int'
        if num > max: # 判断是否比最大值大
            max = num
        sum += num # 将num累加求和
    return (max, sum) # 返回元组


print(max_sum(*(random.randint(0, 100) for i in range(0, 10))))
# 利用生成器表达式生成随机数列作为函数传入参数
```
### 可变长参数
可变长参数是指在调用函数时，传入参数个数可以不固定。
调用函数时，传入参数有两种，一种是位置实参，一种是关键字实参。
分别用\*args和\*\*kwargs接受这两种实参。
args 是 arguments 的缩写，kwargs 则是 keyword arguments 的缩写。
\*args 必须放在 \*\*kwargs 的前面，因为位置参数在关键字参数的前面。
args 的类型是元组，kwargs 的类型是字典。
### 参数解包
在传入参数前加 * 可以将传入序列解包，这个序列可以是可迭代对象。
如果是字典，则默认使用字典的键，若想传入值可以用 values() ，传入键值对可用 items()。
用 ** 可以将字典以关键字实参的形式传入函数。

## 3. 模拟 sorted()
>编写函数，模拟内置函数 sorted()。
### 先简单实现排序，返回一个列表的功能
```python
def sorted_sim1(t):
    # 定义sorted函数
    s = []
    # 创建一个空列表
    for ele in t:
        # 遍历传入列表
        for i, v in enumerate(s):
            # 遍历排序好的列表
            if ele < v:
                # 当当前元素小于列表元素时
                s.insert(i, ele)
                # 在这个位置插入当前元素
                break
                # 停止排序列表的循环
        else:
            s.append(ele)
            # 如果当前元素最大时，放在列表末尾
    return s
    # 返回排序列表
```
### 实现key和reverse参数
```python
def sorted_sim2(t, reverse=False, key=None):
    # 将key和reverse参数默认指定为空
    s = []
    for ele in t:
        ele = key(ele) if key else ele
        # 如果存在key，就对ele进行运算，否则保持不变
        for i, v in enumerate(s):
            v = key(v) if key else v
            # 如果存在key，就对v进行运算，否则保持不变
            if ele > v if reverse else ele < v:
            # 如果reverse是True就降序排列
                s.insert(i, ele)
                break
        else:
            s.append(ele)
    return s
```
### 三目运算符
类似``ele = key(ele) if key else ele``这样的用法称为三目运算符，
类似于c语言中``ele = key?key(ele):ele``。
这样写等价于
```python
if key:
    ele=key(ele)
else:
    ele=ele
```
但是要简洁很多，对于这种简单的if-else语句还是写成三目运算符比较好。
如果逻辑较为复杂，虽然三目运算符支持复杂的嵌套，但是代码可读性会大大降低。
因此复杂的结构还是展开写比较好。

三目运算符是右结合的，所以要从最右面开始判断。
## 4. 通讯录
>用字典建立一个通讯录，向字典中添加和删除通讯人（名字、电话、邮箱、工作单位等），查询某个人的信息，然后输出通讯录中所有人的信息。
### 实现功能
1. 能将通讯录储存，再次运行的时候可以读取通讯录
2. 通讯录可以自定义键值对，从而储存任意信息
3. 添加联系人
4. 修改联系人信息
5. 删除联系人
6. 查询信息，包括查看某个人和所有人
### 代码
```python
import json
# 用到json库储存通讯录信息
import os.path
# 用于判断json文件是否存在


def save_dic(dic, file='Address.json'):
    # 保存通讯录信息到json文件
    with open(file, 'w') as f:
        json.dump(dic, f)
        print('保存成功')


def load_dic(file='Address.json'):
    # 从json文件读取通讯录信息
    if os.path.isfile(file):
        # 如果文件存在就读取
        with open(file, 'r') as f:
            return json.load(f)
    else:
        # 不存在返回空字典
        return {}

#上述两个json用法都是网上看到的，并不完全理解 

def add_dic(dic, name, names):
    # 定义添加联系人的函数
    if name not in dic:
        # 如果之前联系人不存在就创建一个新联系人
        dic[name] = {}
        # 用字典嵌套字典来模拟通讯录
        names += ',' + name
        # 用于修改联系人显示序列，方便用户操作
        print('成功添加联系人')
        # 打印提示信息
        return names
        # 返回联系人序列
    else:
        # 如果联系人存在
        print('联系人已存在')
        # 打印提示信息
        return names
        # 返回联系人序列


def change_dic(dic, name, key, value):
    # 定义改变联系人信息的函数
    dic[name][key] = value
    # 改变字典
    print('写入成功')
    # 打印提示信息


def del_dic(dic, name):
    # 定义删除联系人的函数
    del dic[name]
    # 从字典删除联系人
    names = ','.join((name for name in dic))
    # 重新生成联系人序列
    print('成功删除联系人')
    # 打印提示信息
    return names
    # 返回联系人信息


def show_dic(dic, name=''):
    # 定义查看联系人信息函数
    # 函数有两个功能，一个是查看所有人信息，一个是查看指定人信息
    # 当查看所有人信息时，不改变name的默认值，name为空
    if not name:
        # 查看所有人信息
        for name, info in dic.items():
            # 遍历每个联系人
            print(name)
            for key, value in info.items():
                # 遍历每个联系人的信息
                print(key, ':', value)
    else:
        if name in address:
            # 查看某个人的信息
            print(name)
            # 遍历这个人的信息
            for key, value in dic[name].items():
                print(key, ':', value)
        else:
            # 联系人不存在打印错误信息
            print('未找到联系人')



address = load_dic()
# 加载通讯录
names = ','.join((name for name in address))
# 生成联系人序列
while True:
    print('当前联系人：%s' % names)
    command = input(
        '请输入想要进行的操作\n'
        '1. 添加联系人\n'
        '2. 编辑联系人信息\n'
        '3. 删除联系人\n'
        '4. 查看通讯录\n'
        '5. 保存并退出\n'    
    )
    # 选择操作
    if command == '1':
        # 添加联系人
        name = input('请输入想要添加的联系人，输入exit退出：')
        # 如果不想添加可以退出
        if name == 'exit':
            continue
        else:
            names = add_dic(address, name, names)
            # 这里返回的是新的联系人列表
    elif command == '2':
        # 修改联系人信息
        name = input('请输入想要编辑的联系人，输入exit退出：')
        # 如果不想修改可以退出
        if name == 'exit':
            continue
        elif name not in address:
            # 联系人不存在可以选择是否添加
            command = input('联系人不存在，是否添加（Y/N）').upper()
            # 用 upper() 转换大小写防止输入错误
            if command == 'Y':
                names = add_dic(address, name, names)
                # 如果添加联系人的话可以继续修改信息
            elif command == 'N':
                continue
                # 不添加就直接退出
            else:
                print('输入错误')
                continue
        while True:
            # 不结束可以一直修改信息，如果一次只能修改一条，那么每修改一条就要重新输入一遍联系人
            key = input('请输入想要添加或修改的信息，输入finish结束：')
            if key == 'finish':
                break
                # 如果是finish就退出
            if key in address[name]:
                # 如果信息存在就提示现在信息是什么，询问是否修改
                print('当前信息为:', address[name][key])
                value = input('是否要修改，如修改请输入修改信息，不修改输入no\n')
            else:
                # 如果不存在直接输入信息，也可以输入no退出防止之前信息输错
                value = input('请输入对应信息，不添加输入no\n')
            if value == 'no':
                continue
            change_dic(address, name, key, value)
            # 用函数改变信息
    elif command == '3':
        # 删除联系人
        name = input('请输入想要删除的联系人，输入exit退出：')
        # 不想删除可以退出
        if name == 'exit':
            continue
        names = del_dic(address, name)
    elif command == '4':
        name = input('请输入要查看的联系人，查看全部请输入all，输入exit退出:')
        # 查看联系人，可以查看所有或者不查看
        if name == 'exit':
            continue
        elif name == 'all':
            # 如果是所有人就不传入name
            show_dic(address)
        else:
            # 如果是指定的人就传入name
            show_dic(address, name)
    elif command == '5':
        # 保存退出
        save_dic(address)
        # 保存
        break
        # 退出
    else:
        # 非法输入提示错误信息
        print('输入错误')
```
## 5. 生成素数
> 用生成器的方式计算任意起止范围内质数的和。质数,又称素数,是大于 1 的自然数，除了 1 和它本身外，不能被其他自然数整除。
### 最简单的素数算法
```python
import math


def primenum(start, end):
    if start <= 2:
        start = 3
        yield 2
        # 2一下不存在素数，所以可以跳过，防止出错
    for i in range(start, end, 2) if start % 2 == 1 else range(start + 1, end, 2):
        # 偶数只有2是素数，因此可以跳过偶数
        for k in range(2, int(math.sqrt(i) + 1)):
        # 遍历判断是否能被小于自己的数整除
            if i % k == 0:
            # 如果有就退出
                break
        else:
            # 遍历结束后说明是素数，用yield生成
            yield i

start, end = 2, 10000
sum = 0
for n in primenum(start, end):
    sum += n
print(sum)
```
### 生成器
生成器函数，在每次调用 next() 的时候执行，遇到 yield 语句返回，再次执行时从上次返回的 yield 语句处继续执行，和生成器表达式类似。
利用生成器可以生成一个无限长的序列，再根据需要读取相应的值，下面这种写法可以很清晰地展现这个特点。
### 利用 filter() 生成素数
这是一个我在网上看到的写法
```python
# 生成一个奇数生成器。
def odd_iter():
    n = 1
    while True:
        n = n + 2
        yield n


# 过滤掉n的倍数的数。
def not_divisible(n):
    return lambda x: x % n > 0


# 获取当前序列的第一个元素，然后删除后面序列该元素倍数的数，然后构造新序列。
def primes():
    yield 2
    it = odd_iter()
    while True:
        n = next(it)
        # 获取it序列的下一个值，it即为素数序列
        yield n
        # 生成素数
        it = filter(not_divisible(n), it)
        # 过滤掉生成素数的倍数

start, end = 2, 10000
sum = 0
for n in primes():
    # 因为这个生成器过滤掉的是素数的倍数，因此要从头开始
    if n >= start and n < end:
        sum1 += n
    elif n > end:
        break
print(sum)
```
首先用了一个生成奇数的生成器，这个生成器是惰性的，所以不 next() 是不会产生下一个值的。
对这个生成器 filter() 以后，并不会遍历生成器选出符合要求的值。
而是在运用 next() 方法后，生成的值会交给 filter() 判断，如果符合就生成，不符合就继续生成，直到符合需求。
所以 filter() 并不会因为生成器是无限序列就陷入死循环。

利用这个特点，就可以写出一个可以生成无限长的素数序列的生成器。

