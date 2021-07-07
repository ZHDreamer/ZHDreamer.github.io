# 7Python 第二次上机作业
## 例4-1  编写函数实现字符串加密和解密，循环使用指定密钥，采用简单的异或算法。
### 思路
用for循环遍历文本，用另一个指针不断循环遍历key，代码如下：
```python
def crypt(source, key):
    result = ''
    i = 0
    l = len(key)
    for ch in source:
        result += chr(ord(ch) ^ ord(key[i]))
        i = (i + 1) % l
    return result
```
与给出示例区别就是示例中利用 cycle() 函数来不断循环key的值
### 示例
```python
def crypt(source, key): 
    # 定义一个加密/解密函数，传入参数为明文或密文和秘钥
    from itertools import cycle # import cycle功能

    result = ''
    temp = cycle(key) 
    # cycle可以将字符串变为一个不断重复的迭代器
    # 从而解决明文或密文和秘钥长度不相等的问题
    for ch in source: # for 循环遍历source，将每个字符进行异或运算
        result = result + chr(ord(ch) ^ ord(next(temp)))
        # next() 可返回迭代器对象当前值
        # ord() 可返回字符串对应ASCII码的值，数值为十进制
        # Python 自带异或运算 ^ 分为三步
            # 把两个十进制整数转换成二进制，并让其长度相同
            # 将两个二进制数按位异或
            # 把结果转为十进制，输出
        # 最后用 chr() 将十进制数转为字符，连接到结果尾部
    return result # 返回结果
```
主函数
```python
source = 'Xidian University' # 明文
key = 'CS' # 秘钥

print('Before Encrypted:' + source)
encrypted = crypt(source, key) # 加密，返回密文
print('After Encrypted:' + encrypted)
decrypted = crypt(encrypted, key) # 解密，返回明文
print('After Decrypted:' + decrypted)
```
执行结果
```
Before Encrypted:Xidian University
After Encrypted:':"=c-:561 *':
After Decrypted:Xidian University
```
加密函数中 for 循环也可以用 zip() 函数
```python
for ch, key in zip(source, temp):
    # zip() 可将可迭代对象打包为一个元组
    # 使for循环能同时遍历两个可迭代对象
    result = result + chr(ord(ch) ^ ord(key))
```
### 迭代器与可迭代对象的区别
可以使用 for 循环迭代的对象都可以被称为可迭代对象（Iterable）。
这些数据结构内部实现了 \_iter_() 或 \_getitem_() 方法。
当使用for循环时，python解释器会将可迭代对象转化为迭代器。<br>
迭代器是一个可以记住遍历的位置的对象。
迭代器对象从集合的第一个元素开始访问，直到所有的元素被访问完结束。
迭代器只能往前不会后退。
迭代器必须实现 \_iter_() 和 \_next_() 方法。
因此迭代器一定是可迭代对象，而可迭代对象不一定是迭代器。<br>
所以迭代器不但可以用 next() 遍历，也可以用 for 循环遍历。
zip() 函数也可以传入迭代器作为参数。
### 按位异或运算
按位异或python自带的异或运算，可以分为三步：

- 把两个十进制整数转换成二进制，并让其长度相同（短的前面补0）；
- 将两个二进制数按位异或（相同时为1，不同时为0）；
- 将结果转换回十进制数，输出。<br>

因此需要把字符串用 ord() 转化为十进制整型才能进行异或运算<br>

按位异或有以下几个特性：<br>
1. x^x = 0
2. x^0 = x
3. (x\^y)^z = x\^(y^z)

因此 source ^ key = result 可以推出 <br>
source \^ key ^ key = result ^ key <br>
source ^ 0 = result ^ key <br>
source = result ^ key <br>
所以加密与解密都可以用异或运算完成，只用定义一个函数就可以完成字符串的加密与解密。<br>

## 例4-2  编写程序，生成大量随机信息
>生成大量随机信息，这在需要获取大量数据来测试或演示软件功能的时候非常有用，不仅能真实展示软件功能或算法，还可以避免泄露真实数据或者引起不必要的争议。
### 思路
利用 randint() 生成随机范围内ASCII码的值，再转为字符串。
但是这样的方法比较难同时生成大小写字母、下划线和指定范围内的汉字，灵活度不如示例给出的方法。
因为 randint() 只能生成连续区间范围内的值，如果有多个不相连的区间，就比较难处理了。
### 示例
```python
import random
import string
import codecs

# 常用汉字Unicode编码表（部分），完整列表详见配套源代码
StringBase = '\u7684\u4e00\u4e86\u662f\u6211\u4e0d\u5728\u4eba'

# 获取随机Email
def getEmail():
    # 常见域名后缀，可以随意扩展该列表
    suffix = ['.com', '.org', '.net', '.cn']
    # 创建一个字符序列，包含大小写字母、数字和下划线
        #string.accii_letters 可以生成字母a-z和A-Z
        #string.digits 可以生成数字0-9
    characters = string.ascii_letters + string.digits + '_'
    # 创建用户名和域名序列，长度为6-12和3-6
        # choice() 可返回列表元组或字符串对象中的随机项
        # 利用生成器表达式生成一个可迭代对象作为join的传入参数
    username = ''.join(
        (
            random.choice(characters) 
            for i in range(random.randint(6, 12))
        )
    )
    domain = ''.join(
        (
            random.choice(characters) 
            for i in range(random.randint(3, 6))
        )
    )
    # return值连接用户名，@，域名和域名后缀，选择域名后缀可同样用choice方法
    return username + '@' + domain + random.choice(suffix)

# 获取随机电话号码
def getTelNo():
    # 同样适用生成器表达式生成可迭代对象，然后用 join() 连接
    # join() 函数的传入序列必须为字符串，因此要将生成的随机数转化为字符串
    return ''.join((str(random.randint(0, 9)) for i in range(11)))

# 获取随机姓名或地址
def getNameOrAddress(flag):
    # flag=1表示返回随机姓名，flag=0表示返回随机地址
    # 先定义一个空字符串，方便之后连接字符
    result = ''
    if flag == 1:
        # 大部分中国人姓名在2-4个汉字
        rangestart, rangeend = 2, 4
    elif flag == 0:
        # 假设地址在10-30个汉字之间
        rangestart, rangeend = 10, 30
    else:
        # 防止flag不为0或1导致程序出错
        print('flag must be 1 or 0')
        return ''
    for i in range(random.randint(rangestart, rangeend)):
        # StringBase 为之前定义的Unicode序列
        # 从中随机选择即可的到随机汉字序列
        result += random.choice(StringBase)
    '''
    for 循环也可和上面一样改为生成器表达式形式
    result = ''.join(
        (
            random.choice(StringBase) 
            for i in range(random.randint(rangestart, rangeend))
        )
    )
    '''
    return result

# 获取随机性别
def getSex():
    # choice从('男', '女')这个元组中随机选择
    return random.choice(('男', '女'))

# 随机获得年龄，返回值为字符串形式，年龄区间为[18,100]
def getAge():
    return str(random.randint(18, 100))

# 定义main函数，作为生成随机信息并写入文件的主函数
def main(filename):
    # with 语句适用于对资源进行访问的场合，
    # 确保不管使用过程中是否发生异常都会执行必要的“清理”操作，释放资源，
    # 比如文件使用后自动关闭／线程中锁的自动获取和释放等。
    # codecs.open() 第一个参数为要打开的文件名
    # 第二个参数'w'为仅写入模式打开
        # 如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除
        # 如果该文件不存在，创建新文件
    # 第三个参数'utf-8'为文件编码格式
    # 返回对象为file
    with codecs.open(filename, 'w', 'utf-8') as fp:
        #文件中先写入对应信息顺序
        fp.write('Name,Sex,Age,TelNO,Address,Email\n')
        # 随机生成200个人的信息
        for i in range(200):
            name = getNameOrAddress(1)
            sex = getSex()
            age = getAge()
            tel = getTelNo()
            address = getNameOrAddress(0)
            email = getEmail()
            # 用 join() 连接生成的信息
            line = ','.join([name, sex, age, tel, address, email]) + '\n'
            # 将生成信息写入文件
            fp.write(line)

# 定义一个输出函数，用于print生成的信息
def output(filename):
    # 用只读模式打开文件，编码格式仍为'utf-8'，文件指针在文件的开头
    with codecs.open(filename, 'r', 'utf-8') as fp:
        # 循环直到整个文件都被读取后return
        while True:
            # 读取一行的值，包括换行符，之后再执行会自动读取下一行
            line = fp.readline()
            # 如果整个文件都读取了一遍，readline返回值为''
            # 此时 not line 为 True，函数返回中断循环
            if not line:
                return
            # 以逗号为分割符分割line，返回一个分割后的列表
            line = line.split(',')
            # 用for循环遍历列表再print每个值，用逗号分隔
            for i in line:
                print(i, end=',')
            # 每print一行就换行
            print()
'''
实际上上述代码存在错误，readline()是会读取换行符的
因此line最后是有换行符的，这个换行符并未消除，执行效果如下
Name,Sex,Age,TelNO,Address,Email
,
可以看到email后面先进行了换行，因为源文件这里有换行符，最后也多了一个逗号
实际上先将字符串split()再遍历的操作其实非常没有必要
可以用 strip() 去掉line的换行符后直接print，代码为

def output(filename):
    with codecs.open(filename, 'r', 'utf-8') as fp:
        while True:
            line = fp.readline().strip()
            if not line:
                return
            print(line)

或者用end=''直接利用读取的换行符进行输出，代码为

def output(filename):
    with codecs.open(filename, 'r', 'utf-8') as fp:
        while True:
            line = fp.readline()
            if not line:
                return
            print(line, end='')

'''

# 如果脚本独立运行，__name__属性为__main__，就执行if中代码
if __name__ == '__main__':
    # 定义文件名
    filename = 'information.txt'
    # 用main函数生成并写入随机信息
    main(filename)
    # 用output函数输出信息
    output(filename)
```
### 列表推导式与生成器表达式
生成器表达式的写法与列表推导式几乎一样，只需把[]改为()。
生成器表达式生成一个生成器对象。
和列表推导式不同的是，这个对象不被调用时不进行任何操作，因此很节省系统资源。
生成器对象可以用for循环遍历，因此是可迭代的，也可以用 next() 遍历，因此生成器对象也是迭代器。

## 例4-3  检查并判断密码字符串的安全强度
### 思路
和示例思路差不多，但是未必想的到用in的方法来判断是否满足某个条件，和利用字典储存密码强度与满足条件数的对应关系。
### 示例
```python
import string
# 导入字符串库
    # string.digits 是所有数字
    # string.ascii_lowercase 是所有小写字母
    # string.ascii_uppercase 是所有大写字母

def check(pwd):
    # 密码必须至少包含6个字符
    # isinstance 判断传入密码是否是字符串格式
    # 如果密码字符少于6个就返回错误信息
    if not isinstance(pwd, str) or len(pwd) < 6:
        return 'not suitable for password'
    # 密码强度等级与包含字符种类的对应关系
    # 创建一个哈希表来储存对应关系
    d = {1: 'weak', 2: 'below middle', 3: 'above middle', 4: 'strong'}
    # 分别用来标记pwd是否含有数字、小写字母、大写字母和指定的标点符号
    r = [False] * 4
    for ch in pwd:
        # 是否包含数字
        if not r[0] and ch in string.digits: # 当r[0]为True时跳过判断，下同 
            r[0] = True
        # 是否包含小写字母
        elif not r[1] and ch in string.ascii_lowercase:
            r[1] = True
        # 是否包含大写字母
        elif not r[2] and ch in string.ascii_uppercase:
            r[2] = True
        # 是否包含指定的标点符号
        elif not r[3] and ch in ',.!;?<>':
            r[3] = True
        # 统计包含的字符种类，返回密码强度
    # 获得r中True的个数即为密码满足的条件个数
    # get()可以返回字典中对应键的值，如果没找到返回默认信息，这里设定为'error'
    return d.get(r.count(True), 'error')


print(check('a2Cd,'))
```
### isinstance() 和 tpye() 的区别
type() 不会认为子类是一种父类类型，不考虑继承关系。
isinstance() 会认为子类是一种父类类型，考虑继承关系。
当 type() 传入参数只有一个时，会返回类名。
isinstance() 传入变量和类名，如果匹配返回True，否则返回False。
### 程序的改进
原程序用一个列表储存密码的满足条件，当密码满足四个条件时可以退出循环。

改进后的程序：
```python
import string

def check(pwd):
    if not isinstance(pwd, str) or len(pwd) < 6:
        return 'not suitable for password'
    d = {1: 'weak', 2: 'below middle', 3: 'above middle', 4: 'strong'}
    r = [False] * 4
    c = 0
    for ch in pwd:
        # 只用r标记是否满足某个条件，c储存满足条件个数
        if c = 4:
            break
        if not r[0] and ch in string.digits:
            r[0] = True
            c += 1
        elif not r[1] and ch in string.ascii_lowercase:
            r[1] = True
            c += 1
        elif not r[2] and ch in string.ascii_uppercase:
            r[2] = True
            c += 1
        elif not r[3] and ch in ',.!;?<>':
            r[3] = True
            c += 1
    return d.get(c, 'error')


print(check('a2Cd,'))
```
## 例4-4 句子倒置
>编写程序，把一个英文句子中的单词倒置，标点符号不倒置，例如 I like beijing. 经过函数后变为：beijing. like I
### 思路
和第二种方法思路差不多，先将字符串分割为列表，然后将列表逆序再转化为字符串
### 示例
给出了三种解法<br>
第一种
```python
def rev1(s):
    return ' '.join(reversed(s.split()))
    # 先将单词用 split() 分隔，变为列表形式
    # 再用 reversed() 逆序
    # 最后用 join() 以空格为间隔连接单词
```
第二种
```python
def rev2(s):
    t = s.split()
    t.reverse()
    return ' '.join(t)
    # 先将单词用 split() 分隔，变为列表形式
    # 再用 revers() 原地操作逆序
    # 最后用 join() 以空格为间隔连接单词
```
第三种
```python
def rev5(s):
    '''字符串整体逆序，分隔，再各单词逆序'''
    t = ''.join(reversed(s)).split()
    # 先将字符串整体逆序，用 split() 分隔为列表
    t = map(lambda x:''.join(reversed(x)), t)
    # 用map对于每个字符逆序，reversed的结果是迭代器，因此需要用join转化为字符串
    return ' '.join(t)
    # 再用join连接每个单词
```
第一种写法比较简洁美观，但是理解需要一点时间。
第二种写法最为直观，容易理解
第三种写法比较冗长复杂，也不好理解。
### reverse() 与 reversed() 的区别
reverse() 是针对列表的逆序方法，对于字符串无效，而且是原地操作，无返回值。<br>
reversed() 可以翻转列表、字符串等序列，返回一个迭代器。<br>
因此第三种方法中``t = map(lambda x:''.join(reversed(x)), t)``不能写成``t = map(lambda x:reversed(x), t)``
因为会向下一句的 join() 中传入 map() 的迭代器后再传入一个迭代器，此时 join() 就会报错``sequence item 0: expected str instance, reversed found``应为str实例，但是找到了reversed。
而像第一句那样的写法就不会报错。

## 例4-5  编写程序，查找一个字符串中最长的数字子串
### 思路
与第二种方法类似，当遇到数字就开始记录，数字结束停止，计算序列长度，如果大于最大值就保存最大值。
### 示例
第一种方法
```python
def longest(s):
    result = [] # 储存所有数字序列
    t = [] # 临时储存单个数字序列
    for ch in s:                      # 遍历字符串中所有字符
        if '0'<=ch<='9':              # 遇到数字，记录到临时变量
            t.append(ch)
        elif t:
            result.append(''.join(t)) # 遇到非数字，把临时的连续数字记下来
            t = []                    # 清空临时变量
    if t:                             # 考虑原字符串以数字结束的情况
        result.append(''.join(t))
        
    if result:
        return max(result, key=len)   # 比较各个数字序列长度，返回最长串
    return 'No'
```
第二种方法
```python
def longest(s):
    length = len(s)
    # 获取序列长度
    start = 0
    # 设置起始指针
    span = (0, 0)
    # 设置储存双指针的元组，因为后面需要比较序列长度
    # 在第一个数字序列获取之前需要有一个长度为0的区间
    for pos in range(length):
        # 用for循环创建第二个指针
        if s[pos].isdigit() and (pos == 0 or not s[pos - 1].isdigit()):
            # 当当前是数字，且前一个不是数字或者这是第一个字符，此时为数字序列开始
            # 数字序列开始时，记录当前位置
            start = pos
        elif (
            (not s[pos].isdigit())
            and s[pos - 1].isdigit()
            and pos - start > span[1] - span[0]
        ):
            # 当当前是非数字，且前一个是数字，此时为数字序列结束
            # 数字序列结束时，比较序列长度
            # 如果当前为最长序列，记录起始结束指针
            span = (start, pos - 1)
    # 字符串以数字结束的情况
    if s[pos].isdigit() and pos - start >= span[1] - span[0]:
        span = (start, pos)
    # 用切片的方式返回结果
    return s[span[0] : span[1] + 1]
```
第一种方法看着简洁清晰，但是占用内存较大
第二种方法比较复杂，但是不用占用额外空间，且运行速度稍快
### max() 函数中key参数的使用
max() 函数默认会返回所给出的序列中的最大值。
key 可以指定比较方式，比如这个程序中``max(result, key=len)``就比较了数字串的长度。
key 也可以用匿名函数 lambda 来指定比较方式。
比如``max(list, key=lambda x: -x**2+3*x)``可以比较列表里面对函数$f(x) = -x^2+3x$的最大值点。