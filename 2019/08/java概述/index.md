# Java概述


Java概述

<!--more-->


## JRE与JDK
**JRE(Java Runtime Environment Java运行环境)**
包括Java虚拟机（JVM Java Virtual Machine）和Java程序所需的核心类库等。
要行运行一个Java程序，计算机中只需要安装JRE即可。

**JDK(Java Development Kit Java开发工具包)**
JDK是提供给Java开发人员使用的，其中包含了Java的开发工具，也包括了JRE，所以安装了JDK，就不用再单独安装JRE了。
包含有编译工具（javac.exe）打包工具（jar.exe）等

## 数据类型
![数据类型](https://raw.githubusercontent.com/Kiasma1/Kiasma1.github.io/master/posts/2019/java/数据类型.png)

###  常量

```java
final double CM_PER_INCH = 2.54 //常量名习惯用大写
```

在同一个类的其他方法中也可以使用这个变量：

```java
public static final double CM_PER_INCH = 2.54 //定义了一个类常量

```

### 数学

```java
import static java.lang.Math.*;
```

### 枚举类型

```java
enum Size { SMALL, MEDIUM, LARGE, EXTRA_LARGE };
Size s = Size.MEDIUM;
```

## 字符串

每个用双引号括起来的字符串的都是String类的一个实例：

```java
String e = "";
String greeting = "Hello";
```

### 子串

```java
String greeting = "Hello";
String s = greeting.substring(0, 3);
```

### 字符串拼接

```java
int age = 13;
String rating = "PG" + age;
System.out.println("The answer is" + answer);
```

```java
//需要把多个 字符串放在一起，用一个定界符分隔，可以使用静态join方法：
String all = String.join(" / ", "S", "M", "L", "XL");
// all is the string "S / M / L / XL"
```

### 不可变字符串

String被默认为不可变字符串

如果复制一个字符串变量，原始字符串与复制的字符串共享相同的字符。

但是如果要更改一个字符串，只能新创建一个字符串，然后赋给字符串变量

### 检测字符串是否相等

````java
s.equals(t);
"Hello".equals(greeting);		//区分大小写
"Hello".equalsIgnorCase("hello"); //不区分大小写
````

**一定不能使用 == 运算符检测两个字符串是否相等！**

这个运算符只能确定两个字符串是否放置在同一个位置上。而且完全由可能将内容相同的多个字符串的拷贝放置在不同的位置上。

实际上只有字符串常量是共享的，而+或者substring等操作产生的结果并不是共享的。

### 空串与Null串

空串 "" 是长度为0的字符串。

null串 表示没有任何对象与该变量关联。

## 输入输出

### 读取输入

```java
import java.util.*;
Scanner in = new Scnanner(System.in);
System.out.print("What is your name? ");
String name = in.nextline(); //要想读取一个单词（以空白符为分隔符），就调用next()
//读取一个整数就调用nextInt()
//同理 nextDouble()
```

### 格式化输出

```java
System.out.printf("%8.2f",x);
System.out,printf("Hello, %s.", name);
//格式化构建String
String message = String.format("Hello, %s.", name);
```

### 文件输入与输出

```java
Scanner in = new Scanner(Paths.get("myfile.txt"), "UTF-8");//接下来就可以读取了
PrintWriter out = new PrintWrite("myfile.txt", "UTF-8"); //写入
```

## 大数值

java.math 包中的 BigInteger 和 BigDecimal 两个类

```java
BigInteger a = BigInteger.valueOf(100);
BigInteger c = a.add(b); //c = a + b
BigInteger d = c.multiply(b.add(BigInteger.valueOf(2))); // d = c * (b + 2)
```



## 数组

```
int[] a;
int[] a = new int[100];
```

### foe each 循环

```java
for(int element : a)
    System.out.println(element);
```

### 数组初始化

```java
int[] a = {2, 3, 5, 7, 11 };
//匿名数组
new int[] { 2, 3, 4, 5, 6 };
```

### 数组拷贝

在Java中，允许将一个数组变量拷贝给另一个数组变量。这时，两个变量将引用同一个数组

```java
int[] b = a;
b[3] = 12; //现在a[3]也成为了12
```

如果希望将数组所有值拷贝到一个新的数组中去就要使用Arrays类的copyOf方法：

```java
int[] c = Arrays.copuOf(a, a.lenght);
```

### 数组排序

```java
int[] a = { 2,3123,213,13,23 };
Arrays.sort(a);
```
