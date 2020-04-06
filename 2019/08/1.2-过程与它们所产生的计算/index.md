# 1.2 过程与它们所产生的计算


过程与它们所产生的计算

<!--more-->

## 1.2 过程与它们所产生的计算

### 递归计算过程

**注意区分递归过程和递归计算过程：**

 计算过程由一个推迟执行的运算链条刻画，称为**递归计算过程**

语法上，如果这个过程定义中引用了该过程本身，则称它为**递归过程**

```scheme
(define factorial n)
	(if (= n 1)
        1
        (* n (factorial (- n 1)))))
(factorial 3)
其代换模型：
(factorial 3)
(* 3 (factorial 2))
(* 3 (* 2 (factorial 1)))
(* 3 (* 2 1))
(* 3 2)
6
```

更形式的说，我们要维持一个变动中的乘积product，以及一个从1到n的计数器counter，上面这一计算过程就可以描述为counter和product的如下变化。

product <- counter * product

counter <- counter + 1

于是我们又可以依照这个描述重构一个计算阶乘的过程：

```scheme
(define (factorial n)
  (fact-iter 1 1 n))
(define (fact-ier product counter max-count)
  (if (> counter max-count)
      product
      (fact-iter (* counter product) (+ counter 1) max-count)))
(factorial 3)
其代换模型为：
(factorial 3)
(fact-iter 1 1 3)
(fact-iter 1 2 3)
(fact-iter 2 3 3)
(fact-iter 6 4 3)
6
```

上面这种计算过程被称为**迭代计算过程**

迭代计算过程中没有任何增长或者收缩，我们将需要保存的东西保存在变量之中，因为变量的个数是固定的，所以一般来说，迭代过程就是那种其状态可以用**固定数目的状态变量**描述的计算过程。

而递归计算过程中，推迟计算的链条越长，需要保存的信息也就越多。

与c语言不同的是，scheme中总能在常量空间中执行迭代型计算过程，即使这一计算是用一个递归过程描述的。具有这种特性的实现称为**尾递归**。



### 树形递归

```scheme
(define (fib n)
  (cond ((= n 0) 0)
        ((= n 1) 1)
        (else (+ (fib (- n 1))
                 (fib (- n 2))))))
```

上面一段代码的展开过程为一个树形，但这种方法很糟糕，因为它做了很多冗余的计算。

我们将他转化为迭代

a+b->a

a->b

```scheme
(define  (fib n)
  (fib-iter 1 0 n))

(define (fib-iter a b count)
  (if (= count 0)
      b
      (fib-iter (+ a b) a (- count 1))))
```


