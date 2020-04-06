# 1.3 用高阶函数做抽象


 用高阶函数做抽象

<!--more-->

## 1.3 用高阶函数做抽象

这里先放出三个例子：

**第一个**：计算从a到b各个整数之和

$$
\sum_{k=a}^{b} k=a+\cdots+b
$$


```lisp
(define (sum-integers a b)
    (if (> a b)
        0
        (+ a (sum-integers (+ a 1) b))))
```


**第二个**：计算从a到b各个整数的立方之和


$$
\sum_{k=a}^b k^3=a^3+\dots+b^3
$$


```lisp
(define (sum-cubes a b)
    (if (> a b)
        0
        (+ (cube a) (sum-cubes (+ a 1) b))))
```



**第三个**：计算下面序列之和


$$
\frac{1}{1 \cdot 3}+\frac{1}{5 \cdot 7}+\frac{1}{9 \cdot 11}+\cdots
$$


```lisp
(define (pi-sum a b)
    (if (> a b)
        0
        (+ (/ 1.0 (* a (+ a 2))) (pi-sum (+ a 4) b))))
```


我们将上面三个程序最后一行提取出来总的分析一下：


![图1](F:\github博客\Kiasma1.github.io\posts\2019\SICP\tu1.png)


不难可以概括出：


```lisp
(define (<name> a b)
    (if (> a b)
        0
        (+ (<term> a)
           (<name> (<next>  a) b))))
```


数学家很早就认识到序列求和中的抽象模式，并提出了“求和记法”，例如：


$$
\sum_{n=a}^b f(n)=f(a)+\dots+f(b)
$$


于是我们将其中的“空位”翻译为形式参数：

```lisp
(define (sum term a next b)
    (if (> a b)
        0
        (+ (term a)
           (sum term))))
```


依据上面
我们可以重新写一下第二第三的程序


```lisp
(define (inc n) (+ n 1))
(define (cube n) (* n n n))
(define (sum-cubes a b)
    (sum cube a inc b))
```


