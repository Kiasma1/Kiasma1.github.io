# Python Day2


<!--more-->

# Python-day2

## 1. 各种内置数据结构

### 1.1 list和tuple

#### 1.1.1 list(列表)

list是一种有序的集合，可以随时添加和删除其中的元素。

```python
>>> classmates = ['Michael', 'Bob', 'Tracy']
>>> classmates
['Michael', 'Bob', 'Tracy']
```

##### 访问list

```python
>>> classmates[0]
'Michael'
>>> classmates[1]
'Bob'
>>> classmates[2]
'Tracy'
>>> classmates[3]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list index out of range

>>> classmates[-1]
'Tracy'

>>> classmates[-2]
'Bob'
>>> classmates[-3]
'Michael'
>>> classmates[-4]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list index out of range
```

##### 获取list长度

```python
>>> len(classmates)
3
```

##### 对list进行操作

```python
#在尾添加元素
>>> classmates.append('Adam')
>>> classmates
['Michael', 'Bob', 'Tracy', 'Adam']

#在给定位置添加元素
>>> classmates.insert(1, 'Jack')
>>> classmates
['Michael', 'Jack', 'Bob', 'Tracy', 'Adam']

#删除尾元素
>>> classmates.pop()
'Adam'
>>> classmates
['Michael', 'Jack', 'Bob', 'Tracy']

#删除给定位置元素
>>> classmates.pop(1)
'Jack'
>>> classmates
['Michael', 'Bob', 'Tracy']

#赋值
>>> classmates[1] = 'Sarah'
>>> classmates
['Michael', 'Sarah', 'Tracy']
```

**list里面的元素的数据类型也可以不同，list元素也可以是另一个list。**

#### 1.1.2 tuple(元组)

tuple和list非常类似，但是tuple一旦初始化就不能修改。

```python
>>> classmates = ('Michael', 'Bob', 'Tracy')
```

**注意点一：要定义一个只有一个元素的tuple `(1,)` 一定要加逗号，不然会有歧义**

**注意点二：tuple中可以含有可变元素，这个元素同样跟以前一样也是可变的（可以理解为tuple中保存的是指向这个可变元素的指针，因此tuple是没有变化的）**

### 1.2 dict(字典)和set(集合)

#### 1.2.1 dict(字典)

dict全称dictionary，在其他语言中也称为map，使用键-值（key-value）存储，具有极快的查找速度。

```python
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d['Michael']
95
```

##### 添加元素

```python
>>> d['Adam'] = 67
>>> d['Adam']
67
```

##### 判断是否存在某元素

- 直接取用

```python
>>> d['Thomas']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'Thomas'
```

- 判断语句

```python
>>> 'Thomas' in d
False
```

- get() 方法

通过dict提供的get()方法，如果key不存在，可以返回None，或者自己指定的value：

```python
>>> d.get('Thomas')
>>> d.get('Thomas', -1)
-1
```

##### 删除元素

```python
>>> d.pop('Bob')
75
```

##### 注意点

和list比较，dict有以下几个特点：

- 查找和插入的速度极快，不会随着key的增加而变慢；
- 需要占用大量的内存，内存浪费多。

而list相反：

- 查找和插入的时间随着元素的增加而增加；
- 占用空间小，浪费内存很少。

所以，dict是用空间来换取时间的一种方法。

dict可以用在需要高速查找的很多地方，在Python代码中几乎无处不在，正确使用dict非常重要，**需要牢记的第一条就是dict的key必须是不可变对象。**

这是因为dict根据key来计算value的存储位置，如果每次计算相同的key得出的结果不同，那dict内部就完全混乱了。这个通过key计算位置的算法称为哈希算法（Hash）。

要保证hash的正确性，作为key的对象就不能变。在Python中，字符串、整数等都是不可变的，因此，可以放心地作为key。而list是可变的，就不能作为key。

#### 1.2.2 set(集合)

要创建一个set，需要提供一个list作为输入集合：

```python
>>> s = set([1, 2, 3])
>>> s
{1, 2, 3}

重复元素在set中自动被过滤
>>> s = set([1, 1, 2, 2, 3, 3])
>>> s
{1, 2, 3}

>>> s.add(4)
>>> s
{1, 2, 3, 4}

>>> s.remove(4)
>>> s
{1, 2, 3}

>>> s1 = set([1, 2, 3])
>>> s2 = set([2, 3, 4])
>>> s1 & s2
{2, 3}
>>> s1 | s2
{1, 2, 3, 4}
```

set和dict的唯一区别仅在于没有存储对应的value，但是，set的原理和dict一样，所以，**同样不可以放入可变对象**，因为无法判断两个可变对象是否相等，也就无法保证set内部“不会有重复元素”。
