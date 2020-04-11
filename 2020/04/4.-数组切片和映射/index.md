# 4. 数组、切片和映射


Go 语言有 3 种数据结构可以让用户管理集合数据：数组、切片和映射。

了解这些数据结构，一般会从数组开始，因为数组是切片和映射的基础数据结构。

<!--more-->

# Ch04 数组、切片和映射

## 4.1 数组的内部实现和基础功能

了解这些数据结构，一般会从数组开始，因为数组是切片和映射的基础数据结构。理解了数
组的工作原理，有助于理解切片和映射提供的优雅和强大的功能。

### 4.1.1 内部实现

在 Go 语言里，数组是一个长度固定的数据类型，用于存储一段具有相同的类型的元素的连
续块。数组存储的类型可以是内置类型，如整型或者字符串，也可以是某种结构类型。

![](http://cdn.shanzei.top/20200410145415.png)

### 4.1.2 声明和初始化

```go
var array [5]int
```

一旦声明，数组里存储的数据类型和数组长度就都不能改变了。如果需要存储更多的元素，就需要先创建一个更长的数组，再把原来数组里的值复制到新数组里。

在 Go 语言中声明变量时，总会使用对应类型的零值来对变量进行初始化。数组也不例外。当数组初始化时，数组内每个元素都初始化为对应类型的零值。

```go
array := [5]int{10, 20, 30, 40, 50}
array := [...]int{10, 20, 30, 40, 50}
array := [5]int{1:10, 2:20}
```

### 4.1.3 使用数组

```go
array := [5]int{10, 20, 30, 40, 50}
array[2] = 35
```

![](http://cdn.shanzei.top/20200410151641.png)

```go
array := [5]*int{0: new(int), 1: new(int)}
*array[0] = 10
*array[1] = 20
```

![](http://cdn.shanzei.top/20200410151705.png)

```go
array1 := [5]string{"Red", "Blue", "Green", "Yellow", "Pink"}
array2 := array1
//长度和元素类型相同的数组才能相互赋值，赋值后是一个新的拷贝
```

![](http://cdn.shanzei.top/20200410151808.png)

```go
array1 := [3]*string{new(string), new(string), new(string)}

var array2 [3]*string
array2 = array1
//或者合并为一句 array2 := array1

*array1[0] = "Red"
*array1[1] = "Blue"
*array1[2] = "Green"
```

![](http://cdn.shanzei.top/20200410152109.png)

### 4.1.4 多维数组

```go
{% raw %}
var array [4][2]int

array := [4][2]int{{10, 11}, {20, 21}, {30, 31}, {40, 41}}

array := [4][2]int{1: {20, 21}, 3: {40, 41}}

array := [4][2]int{1: {0: 20}, 3: {1: 41}}
{% endraw %}
```
![](http://cdn.shanzei.top/20200410153600.png)

### 4.1.5 在函数间传递数组

```go
var array [1e6]int

//按值传递, 在栈上分配 8 MB 的内存
func foo(array [1e6]int){ ... }
foo(array)

//按指针传递, 在栈上分配 8 字节的内存给指针
func foo(array *[1e6]int){ ... }
foo(&array)
```

## 4.2 切片的内部实现和基础功能

切片是一种数据结构，这种数据结构便于使用和管理数据集合。切片是围绕动态数组的概念构建的，可以按需自动增长和缩小。切片的动态增长是通过内置函数 append 来实现的。这个函数可以快速且高效地增长切片。还可以通过对切片再次切片来缩小一个切片的大小。因为切片的底层内存也是在连续块中分配的，所以切片还能获得索引、迭代以及为垃圾回收优化的好处。

### 4.2.1 内部实现

这 3 个字段分别是**指向底层数组的指针、切片访问的元素的个数（即长度）和切片允许增长到的元素个数（即容量）**。后面会进一步讲解长度和容量的区别。

![](http://cdn.shanzei.top/20200410160534.png)

### 4.2.2 创建和初始化

#### 1. make和切片字面量

```go
//make
slice := make([]string, 5) //长度和容量都是5
slice := make([]string, 3, 5) //长度为3 容量为5
//不允许创建容量小于长度的切片
```

```go
//字面量
slice := []string{"Red", "Blue", "Green", "Yellow", "Pink"}
//长度和容量都是 5 个元素
slice := []int{10, 20, 30}
//长度和容量都是 3 个元素
```

#### 2. nil和空切片

```go
//声明nil整型切片
var slice []int
```

![](http://cdn.shanzei.top/20200410170934.png)

```go
//声明空切片
slice := make([]int, 0)
slice := []int{}
```

### 4.2.3 使用切片

#### 1. 赋值和切片的切片

```go
slice := []int{10, 20, 30, 40, 50}
slice[1] = 25
```

```go
slice := []int{10, 20, 30, 40, 50}
newSlice := slice[1:3]
```

![](http://cdn.shanzei.top/20200410171359.png)

![](http://cdn.shanzei.top/20200410171514.png)

切片的切片和原来的切片共用底层数组

![](http://cdn.shanzei.top/20200410171631.png)

#### 2. 切片增长

当append 调用返回时，会返回一个包含修改结果的新切片。函数 append 总是会增加新切片的长度，而容量有可能会改变，也可能不会改变，这取决于被操作的切片的可用容量。

**如果容量不变则前后切片共享底层数组，如果容量改变则会新建底层数组**

```go
slice := []int{10, 20, 30, 40, 50}
newSlice := slice[1:3]
newSlice = append(newSlice, 60)
```

![](http://cdn.shanzei.top/20200410172801.png)

```go
slice := []int{10, 20, 30, 40}
newSlice := append(slice, 50)
```

![](http://cdn.shanzei.top/20200410173853.png)



函数 append 会智能地处理底层数组的容量增长。在切片的容量小于 1000 个元素时，总是会成倍地增加容量。一旦元素个数超过 1000，容量的增长因子会设为 1.25，也就是会每次增加 25%容量。随着语言的演化，这种增长算法可能会有所改变。

#### 3. 创建切片的切片时的第3个索引

```go
source := []string{"Apple", "Orange", "Plum", "Banana", "Grape"}
slice := source[2:3:4]
```

![](http://cdn.shanzei.top/20200410190623.png)

```go
//计算长度和容量
slice[i:j:k] 或 [2:3:4]
//长度：j - i 或 3 - 2 = 1
//容量：k - i 或 4 - 2 = 2
```

#### 4. 将一个切片追加到另一个切片

```go
s1 := []int{1, 2}
s2 := []int{3, 4}

fmt.Printf("%v\n", append(s1, s2...))

Output:
[1 2 3 4]
```

#### 5. 迭代切片

```go
slice := []int{10, 20, 30, 40}
for index, value := range slice {
    fmt.Printf("Index: %d Value: %d\n", index, value)
}

Output:
Index: 0 Value: 10
Index: 1 Value: 20
Index: 2 Value: 30
Index: 3 Value: 40
```

![](http://cdn.shanzei.top/20200410192407.png)

**需要强调的是，range 创建了每个元素的副本，而不是直接返回对该元素的引用。**

```go
// 创建一个整型切片
// 其长度和容量都是 4 个元素
slice := []int{10, 20, 30, 40}
// 迭代每个元素，并显示值和地址
for index, value := range slice {
	fmt.Printf("Value: %d Value-Addr: %X ElemAddr: %X\n",
		value, &value, &slice[index])
}

Output:
Value: 10 Value-Addr: 10500168 ElemAddr: 1052E100
Value: 20 Value-Addr: 10500168 ElemAddr: 1052E104
Value: 30 Value-Addr: 10500168 ElemAddr: 1052E108
Value: 40 Value-Addr: 10500168 ElemAddr: 1052E10C
```

```go
//使用传统的 for 循环对切片进行迭代
// 创建一个整型切片
// 其长度和容量都是 4 个元素
slice := []int{10, 20, 30, 40}

// 从第三个元素开始迭代每个元素
for index := 2; index < len(slice); index++ {
	fmt.Printf("Index: %d Value: %d\n", index, slice[index])
}

Output:
Index: 2 Value: 30
Index: 3 Value: 40
```

### 4.2.4 多维切片

```go
{% raw %}
slice := [][]int{{10}, {100, 200}}
{% endraw %}
```

![](http://cdn.shanzei.top/20200410192741.png)

```go
{% raw %}
// 创建一个整型切片的切片
slice := [][]int{{10}, {100, 200}}
// 为第一个切片追加值为 20 的元素
slice[0] = append(slice[0], 20)
{% endraw %}
```

![](http://cdn.shanzei.top/20200410192914.png)

### 4.2.5 在函数间传递切片

在函数间传递切片就是要在函数间以值的方式传递切片。由于切片的尺寸很小，在函数间复制和传递切片成本也很低。

```go
slice := make([]int, 1e6)

func foo(slice []int) []int{
    ...
    return slice
}

slice = foo(slice)
```

在 64 位架构的机器上，一个切片需要 24 字节的内存：指针字段需要 8 字节，长度和容量字段分别需要 8 字节。由于与切片关联的数据包含在底层数组里，不属于切片本身，所以将切片复制到任意函数的时候，对底层数组大小都不会有影响。复制时只会复制切片本身，不会涉及底层数组

![](http://cdn.shanzei.top/20200410193213.png)

## 4.3 映射的内部实现和基础功能

映射是一种数据结构，用于存储一系列无序的键值对。
映射里基于键来存储值。通过一个例子展示了映射里键值对是如何存储的。映射功能强大的地方是，能够基于键快速检索数据。键就像索引一样，指向与该键关联的值。

![](http://cdn.shanzei.top/20200410193342.png)

### 4.3.1 内部实现

映射是一个集合，可以使用类似处理数组和切片的方式迭代映射中的元素。但映射是无序的集合，意味着没有办法预测键值对被返回的顺序。即便使用同样的顺序保存键值对，每次迭代映射的时候顺序也可能不一样。无序的原因是映射的实现使用了散列表。

![](http://cdn.shanzei.top/20200410235546.png)

### 4.3.2 创建和初始化

```go
//使用make
dict := make(map[string]int)
//使用字面量
dict := map[string]string{"Red": "#da1337", "Orange": "#e95a22"}
```

映射的键可以是任何值。这个值的类型可以是内置的类型，也可以是结构类型，只要这个值
可以使用==运算符做比较。**切片、函数以及包含切片的结构类型这些类型由于具有引用语义，
不能作为映射的键，使用这些类型会造成编译错误。**

### 4.3.3 使用映射

#### 1. 赋值

```go
colors := map[string]string{}
colors["Red"] = "#da1337"
```

可以通过声明一个未初始化的映射来创建一个值为 nil 的映射（称为 nil 映射 ）。nil 映射不能用于存储键值对，否则，会产生一个语言运行时错误。

```go
var colors map[string]string
colors["Red"] = "#da1337"

Runtime Error:
panic: runtime error: assignment to entry in nil map
```

测试映射里是否存在某个键是映射的一个重要操作。这个操作允许用户写一些逻辑来确定是否完成了某个操作或者是否在映射里缓存了特定数据。这个操作也可以用来比较两个映射，来确定哪些键值对互相匹配，哪些键值对不匹配。

#### 2. 检验是否存在某值

```go
//通过返回值
value, exists := colors["Blue"]
if exists {
    fmt.Println(value)
}
//通过获取值判断(没有为零值的值时可以用)
value := colors["Blue"]
if value != "" {
    fmt.Println(value)
}
```

在 Go 语言里，通过键来索引映射时，即便这个键不存在也总会返回一个值。在这种情况下，返回的是该值对应的类型的零值。

#### 3. 迭代映射

```go
// 创建一个映射，存储颜色以及颜色对应的十六进制代码
colors := map[string]string{
	"AliceBlue": "#f0f8ff",
	"Coral": "#ff7F50",
	"DarkGray": "#a9a9a9",
	"ForestGreen": "#228b22",
}

// 显示映射里的所有颜色
for key, value := range colors {
	fmt.Printf("Key: %s Value: %s\n", key, value)
}
```

#### 4. 删除键值

```go
// 删除键为 Coral 的键值对
delete(colors, "Coral")

// 显示映射里的所有颜色
for key, value := range colors {
	fmt.Printf("Key: %s Value: %s\n", key, value)
}
```

### 4.3.4 在函数间传递映射

在函数间传递映射并不会制造出该映射的一个副本。实际上，当传递映射给一个函数，并对这个映射做了修改时，所有对这个映射的引用都会察觉到这个修改。这个特性和切片类似，保证可以用很小的成本来复制映射。

## 4.4 小结

- 数组是构造切片和映射的基石。
- Go 语言里切片经常用来处理数据的集合，映射用来处理具有键值对结构的数据。
- **内置函数 make 可以创建切片和映射，并指定原始的长度和容量。也可以直接使用切片和映射字面量，或者使用字面量作为变量的初始值。**
- 切片有容量限制，不过可以使用内置的 append 函数扩展容量。
- 映射的增长没有容量或者任何限制。
- 内置函数 len 可以用来获取切片或者映射的长度。
- 内置函数 cap 只能用于切片。
- 通过组合，可以创建多维数组和多维切片。**也可以使用切片或者其他映射作为映射的值。
  但是切片不能用作映射的键。**
- **将切片或者映射传递给函数成本很小，并且不会复制底层的数据结构。**
