# Python Day1


<!--more-->

# Python-day1

## 1. 变量

```python
name = "lzc"
id = 1
b = True
```

### 1.1 数据类型

|  类型  |                   举例                    |
| :----: | :---------------------------------------: |
|  整数  |             1、0xff00、-8080              |
| 浮点数 |           1.23、1.23e9、1.23e-5           |
| 字符串 | "I'm ok"、'I am ok'、'''   line1     '''' |
| 布尔值 |                True、False                |
|  空值  |                   None                    |

Python的整数没有大小限制。

Python的浮点数也没有大小限制，但是超出一定范围就直接表示为`inf`。

#### 1.1.1 运算

```python
count += 1
True and False
True or False
not True
9 / 3 >>> 3.0        / 除法 的结果永远为浮点数
10 // 3 >>> 3        // 地板除 的结果永远为整数
10 % 3 >>> 1         余数
b = a
```

**关于赋值**

![](http://cdn.shanzei.top/20200525171558.png)

### 1.2 常量

在Python中，通常用全部大写的变量名表示常量，但事实上仍然是一个变量，**Python根本没有任何机制生成常量**。所以，用全部大写的变量名表示常量只是一个习惯上的用法，

```python
PI = 3.14159265359
```

## 2. 字符串

在最新的Python 3版本中，字符串是以Unicode编码的。

```python
对于单个字符的编码，Python提供了ord()函数获取字符的整数表示，chr()函数把编码转换为对应的字符：
>>> ord('A')
65
>>> ord('中')
20013
>>> chr(66)
'B'
>>> chr(25991)
'文'
>>> '\u4e2d\u6587'
'中文'
```

Python中字符串类型是`str`,在内存中以Unicode表示，一个字符对应若干个字节。如果要在网络上传输，或者保存到磁盘上，就需要把`str`变为以字节为单位的`bytes`。

Python对`bytes`类型的数据用带`b`前缀的单引号或双引号表示：

```python
x = b'ABC'
```

要注意区分`'ABC'`和`b'ABC'`，前者是`str`，后者虽然内容显示得和前者一样，但`bytes`的每个字符都只占用一个字节。

### 2.1 字符串和字节串之间的转化

```python
>>> 'ABC'.encode('ascii')
b'ABC'
>>> '中文'.encode('utf-8')
b'\xe4\xb8\xad\xe6\x96\x87'

>>> b'ABC'.decode('ascii')
'ABC'
>>> b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8')
'中文'
```

### 2.2 字符数和字节数

```python
>>> len('ABC')
3
>>> len('中文')
2

>>> len(b'ABC')
3
>>> len(b'\xe4\xb8\xad\xe6\x96\x87')
6
>>> len('中文'.encode('utf-8'))
6
```

### 2.3 格式化

```python
占位符
>>> 'Hello, %s' % 'world'
'Hello, world'
>>> 'Hi, %s, you have $%d.' % ('Michael', 1000000)
'Hi, Michael, you have $1000000.'

format
>>> 'Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125)
'Hello, 小明, 成绩提升了 17.1%'
```



## 3. 编码

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

第一行注释是为了告诉Linux/OS X系统，这是一个Python可执行程序，Windows系统会忽略这个注释；
第二行注释是为了告诉Python解释器，按照UTF-8编码读取源代码，否则，你在源代码中写的中文输出可能会有乱码。
申明了UTF-8编码并不意味着你的.py文件就是UTF-8编码的，必须并且要确保文本编辑器正在使用UTF-8 without BOM编码：
```

## 4. 循环

### if 语句

```python
if _username == username and _password == password:

elif:

else:

```

### while 语句

```python
python中while也有else语句

count = 0
while count < 3:
    guess_age = int(input("guess age:"))
    if guess_age > age:
        print("think smaller...")
    elif guess_age < age:
        print("think bigger!")
    else:
        print("yes, you got it")
        break
    count += 1
else:
    print("you try too many times")
```

### for 语句

```python
for i in range(10):
    print('loop ', i)
```

```python
python中for也有else语句

count = 0
for i in range(3):
    guess_age = int(input("guess age:"))
    if guess_age > age:
        print("think smaller...")
    elif guess_age < age:
        print("think bigger!")
    else:
        print("yes, you got it")
        break
    count += 1
else:
    print("you try too many times")
```

```python
for i in range(0, 10, 2):
    print('loop ', i)


>>> loop 0
>>> loop 2
>>> loop 4
>>> loop 6
>>> loop 8
```
