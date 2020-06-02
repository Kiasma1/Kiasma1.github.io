# Python 函数


<!--more-->

# Python 函数

## 1.1 自定义函数

```python
def my_abs(x):
    if x >= 0:
        return x
    else:
        return -x
```

## 1.2 参数检查

在自己定义的函数中，python可以帮我们检查传入参数的个数，但是不可以检查传入的参数类型。

因此我们需要自己完善参数类型检查，来抛出错误。

```python
def my_abs(x):
    if not isinstance(x, (int, float)):
        raise TypeError('bad operand type')
    if x >= 0:
        return x
    else:
        return -x
```

## 1.3 返回多个值

```python
import math

def move(x, y, step, angle=0):
    nx = x + step * math.cos(angle)
    ny = y - step * math.sin(angle)
    return nx, ny


>>> x, y = move(100, 100, 60, math.pi / 6)
>>> print(x, y)
151.96152422706632 70.0

>>> r = move(100, 100, 60, math.pi / 6)
>>> print(r)
(151.96152422706632, 70.0)
```

**返回值是一个tuple。**但是，在语法上，返回一个tuple可以省略括号，而多个变量可以同时接收一个tuple，按位置赋给对应的值，所以，Python的函数返回多值其实就是返回一个tuple，但写起来更方便。

## 1.4 空函数

```python
def nop():
    pass
```

# 2. 函数的参数

## 位置参数

```python
def power(x, n):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s

>>> power(5, 2)
25 
```

## 非位置参数

```python
def power(x, n):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s

>>> power(n=2, x=5)
25 
```

## 默认参数

** 定义默认参数要牢记一点：默认参数必须指向不变对象！**

```python
def power(x, n=2):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s

>>> power(5)
25
>>> power(5, 3)
125
```

## 可变参数

可变参数就是传入的参数个数是可变的。

在函数内部，**参数numbers接收到的是一个tuple**，因此，函数代码完全不变。但是，调用该函数时，可以传入任意个参数，包括0个参数：

```python
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum

>>> calc(1, 2)
5
>>> calc()
0
```

Python允许你在list或tuple前面加一个*号，把list或tuple的元素变成可变参数传进去：

```python
>>> nums = [1, 2, 3]
>>> calc(*nums)
14
```

## 关键字参数

可变参数允许你传入0个或任意个参数，这些可变参数在函数调用时自动组装为一个tuple。而关键字参数允许你传入0个或任意个含参数名的参数，**这些关键字参数在函数内部自动组装为一个dict**。请看示例：

```python
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)

>>> person('Michael', 30)
name: Michael age: 30 other: {}

>>> person('Bob', 35, city='Beijing')
name: Bob age: 35 other: {'city': 'Beijing'}

>>> person('Adam', 45, gender='M', job='Engineer')
name: Adam age: 45 other: {'gender': 'M', 'job': 'Engineer'}

>>> extra = {'city': 'Beijing', 'job': 'Engineer'}
>>> person('Jack', 24, **extra)
name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}
```

`**extra`表示把`extra`这个dict的所有key-value用关键字参数传入到函数的`**kw`参数，kw将获得一个dict，注意kw获得的dict是extra的一份拷贝，对kw的改动不会影响到函数外的extra。

## 命名关键字参数

如果要限制关键字参数的名字，就可以用命名关键字参数，例如，只接收city和job作为关键字参数。这种方式定义的函数如下：

```python
def person(name, age, *, city, job):
    print(name, age, city, job)
```

如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符*了：

```python
def person(name, age, *args, city, job):
    print(name, age, args, city, job)
```

# 小结

- Python的函数具有非常灵活的参数形态，既可以实现简单的调用，又可以传入非常复杂的参数。

- 默认参数一定要用不可变对象，如果是可变对象，程序运行时会有逻辑错误！

- 要注意定义可变参数和关键字参数的语法：

- `*args`是可变参数，args接收的是一个**tuple**；

- `**kw`是关键字参数，kw接收的是一个**dict**。

- 以及调用函数时如何传入可变参数和关键字参数的语法：

- 可变参数既可以直接传入：`func(1, 2, 3)`，又可以先组装list或tuple，再通过`*args`传入：`func(*(1, 2, 3))`；

- 关键字参数既可以直接传入：`func(a=1, b=2)`，又可以先组装dict，再通过`**kw`传入：`func(**{'a': 1, 'b': 2})`。

- 使用*args和**kw是Python的习惯写法，当然也可以用其他参数名，但最好使用习惯用法。

- 命名的关键字参数是为了限制调用者可以传入的参数名，同时可以提供默认值。

- 定义命名的关键字参数在没有可变参数的情况下不要忘了写分隔符`*`，否则定义的将是位置参数。
