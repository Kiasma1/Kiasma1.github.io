# md中插入数学公式


如何在md中插入数学公式

<!--more-->

## 在github中插入数学公式

需要在文章所在公式之前加上代码

```html
<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>
```

例子

```html
<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>

$$
J(\theta) = \frac 1 2 \sum_{i=1}^m (h_\theta(x^{(i)})-y^{(i)})^2
$$
```

<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>
$$
J(\theta) = \frac 1 2 \sum_{i=1}^m (h_\theta(x^{(i)})-y^{(i)})^2
$$

## LaTex数学公式

### 1、常用语法

|          字符          |              说明              |                实例                |
| :--------------------: | :----------------------------: | :--------------------------------: |
|           $            |   数学公式前后加$是行内公式    |              $a=x+y$               |
|           $$           |   数学公式前后加$$是独占一行   |             $$a=x+y$$              |
|           \            |            转义字符            |               $\\$$                |
|          \\\           |        值数学公式中换行        |           $a=x+y\\\\b=y$           |
|           _            |         后跟内容为下标         |               $a_i$                |
|           ^            |         后跟内容为上标         |               $a^i$                |
|           {}           |   被括号起来的公式是一组内容   |         $x_{22}$ $x^{y^z}$         |
|         \frac          |              分数              |    $\frac{1}{a}$ 1为分子a为分母    |
|         \sqrt          |              根号              | $\sqrt{xy}+\sqrt[a]{x}$ a为开a次根 |
|        \ldorts         |     跟文本底线对齐的省略号     |         $a_{i\ldorts{n}}$          |
|         \cdots         |     跟文本中线对齐的省略号     |            $i\cdots n$             |
|     \left \ right      | 用于自适应匹配分隔符如{,(,\|等 |       $\left \frac{du}{dx}$        |
|          \sum          |              求和              |        $\sum_{k=1}^{n+1}kx$        |
|          \int          |              积分              |        $\int_a^b {\rm d}x$         |
|          \lim          |              极限              |   $\lim_{n\rightarrow+\infty} n$   |
|   \limits \nolimits    |    强制上下限在上下侧或右侧    |      $\sum\limits_{k=1}^n kx$      |
|  \overline \underline  |          上（下）划线          |          $\overline{a+b}$          |
| \overbrace \underbrace |         上（下）花括号         |  $\overbrace{a+b+\dots+n}_{m个}$   |
|          \vec          |              向量              |             $\vec{a}$              |

### 2、希腊字母

| 大写 | markdown | 小写 | markdown    |
| ---- | -------- | ---- | ----------- |
| A    | A        | α    | \alpha      |
| B    | B        | β    | \beta       |
| Γ    | \Gamma   | γ    | \gamma      |
| Δ    | \Delta   | δ    | \delta      |
| E    | E        | ϵ    | \epsilon    |
|      |          | ε    | \varepsilon |
| Z    | Z        | ζ    | \zeta       |
| H    | H        | η    | \eta        |
| Θ    | \Theta   | θ    | \theta      |
| I    | I        | ι    | \iota       |
| K    | K        | κ    | \kappa      |
| Λ    | \Lambda  | λ    | \lambda     |
| M    | M        | μ    | \mu         |
| N    | N        | ν    | \nu         |
| Ξ    | \Xi      | ξ    | \xi         |
| O    | O        | ο    | \omicron    |
| Π    | \Pi      | π    | \pi         |
| P    | P        | ρ    | \rho        |
| Σ    | \Sigma   | σ    | \sigma      |
| T    | T        | τ    | \tau        |
| Υ    | \Upsilon | υ    | \upsilon    |
| Φ    | \Phi     | ϕ    | \phi        |
|      |          | φ    | \varphi     |
| X    | X        | χ    | \chi        |
| Ψ    | \Psi     | ψ    | \psi        |
| Ω    | \Omega   | ω    | \omega      |

### 3、矩阵的显示

```latex
\left|                --左边的竖线
\begin{array}{lcr}     --一个array的开始, l/c/r表示列的对齐方式左/中/右
a & b & c \\           --&分隔列 \\换行 
d & e & f 
\end{array}            --一个array的结束
\right|               --右边的竖线
```

### 4、三角函数

| 算式   | markdown |
| ------ | -------- |
| sinsin | \sin     |
| coscos | \cos     |
| tantan | \tan     |

### 5、对数函数

| 算式   | markdown  |
| ------ | --------- |
| ln15   | \ln15     |
| log210 | \log_2 10 |
| lg7    | \lg7      |

### 6、关系运算符

| 运算符 | markdown |
| ------ | -------- |
| ±      | \pm      |
| ×      | \times   |
| ÷      | \div     |
| ∑      | \sum     |
| ∏      | \prod    |
| ≠      | \neq     |
| ≤      | \leq     |
| ≥      | \geq     |
