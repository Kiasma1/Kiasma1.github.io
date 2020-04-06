# 1.1 程序设计的基本元素


关于程序设计语言中的基本要素

<!--more-->


## 1.1 程序设计的基本元素
**强有力的程序设计语言**
1.不仅是一种指挥计算机执行任务的方式。
2.它还应该成为一种框架，使我们能够在其中组织自己有关计算过程的思想。

而且还应该具有下面三种机制：
- **基本表达形式**，用于表示语言所关心的最简单的个体
- **组合的方法**，通过它们可以从较简单的东西出发构造出**复合的元素**
- **抽象方法**，通过它们可以为复合对象命名，**并将它们当作单元去操作**

**数据**是一种我们希望去操作的“东西”
**过程**就i是有关操作这些数据的规则的描述
任何强有力的程序设计语言都必须能表述**基本的数据和基本的过程**，还需要提供对过程和数据进行**组合和抽象**的方法。

### 几种语法
```scheme
1.(+ 137 349)
2.(define pi 3.1415)
3.(define (square x) (* x x)) == (define square (lambda (x)(* x x)))
4.(define (abs x)
	(cond ((> x 0) x)
			((= x 0) 0)
			((< x 0) (- x))))
```

### 应用序和正则序求值模型
应用序求值：先求值参数而后应用
```scheme
(+ (square (+ 5 1)) (square (* 5 2)))
(+ (square 6) (square 10))
(+ (* 6 6)(* 10 10))
(+ 36 100)
136
```
正则序求值：完全展开而后归约
```scheme
(+ (square (+ 5 1)) (square (* 5 2)))
(+ (* (+ 5 1) (+ 5 1)) (* (* 5 2) (* 5 2)))
(+ (* 6 6) (* 10 10))
(+ 36 100)
136
```
**检验一个解释器是用应用序还是正则序**：
```scheme
( define (p) (p) )
( define (test x y)
    ( if (= x 0)
         0
         y ) )
```
进行求值：（ test 0 （p））
采用正则序求值：(test 0 (p)) ---> ( if (= 0 0) 0 (p) ) ---> 0
采用应用序求值：(test 0 (p)) ---> ( test 0 (p)) ---> (test 0 (p)) --->... ... （无限循环）

### 实例：采用牛顿法求平方根
#### 过程作为黑箱对象（过程抽象）
![sqrt-iter](https://raw.githubusercontent.com/Kiasma1/Kiasma1.github.io/master/posts/2019/SICP/sqrt-iter.png)
```scheme
#lang sicp

(define (squart x)
  (* x x))

(define (abs x)(if (< x 0)
                   (- x)
                   x))

(define (average x y)
  (/ (+ x y) 2))

(define (improve guess x)
  (average guess (/ x guess)))

(define (good-enough? guess x)
  (< (abs (- (squart guess) x)) .001))

(define (sqrt-iter guess x)
  (if (good-enough? guess x)
      guess
      (sqrt-iter (improve guess x) x)))

(define (sqrt x)
  (sqrt-iter 1 x))

(sqrt 25)
(sqrt 2)
(sqrt 4)
```

