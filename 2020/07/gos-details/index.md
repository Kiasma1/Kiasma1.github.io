# Go's Details


记录一些关于Go语言的底层细节

<!--more-->

# Go's details

## 我们通常用var来赋给零值

在声明变量时，如果我们想使用零值初始化变量，我们一定要使用`var`关键字，
而不要使用`:=`这个操作将一个你所“认为”的零值初始化给变量例如`s := string{}`。

通常我们认为`var`和零值是绑定的。

## 关于内置数据结构

### Array

```go
var strings [5]string
strings[0] = "Apple"
```

![](http://cdn.shanzei.top/20200709013035.png)

如图所示我们给予一个字面量赋值给数组的第一个值，**字面量声明了一个string结构，该结构的第一个元素为一个指针，指向底层存放字符的数组。**

**数组中的第一个元素跟字面量的数据相同，因此也会指向同一个底层数组。**

### Slice

```go
var a []string
a = append(a, "APPLE")
a = append(a, "APP")
a = append(a, "A")
```

![](http://cdn.shanzei.top/20200713094338.png)

**注意**：扩充后的元素的位置已经改变，因此我们最好不要保存切片中某一元素的地址，然后用地址改变数据。如果出现底层数组扩充，那么地址和原地址将会不同。

```go
a1 := make([]string, 3, 4)
a1 = append(a1, "APPLE")
a1 = append(a1, "APP")
a1 = append(a1, "A")
fmt.Println(a1, len(a1), cap(a1))
for k,v := range a1{
	fmt.Println(k,v)
}

var a2 []string
a2 = append(a2, "APPLE")
a2 = append(a2, "APP")
a2 = append(a2, "A")
fmt.Println(a2, len(a2), cap(a2))
for k,v := range a2{
	fmt.Println(k,v)
}
```

![](http://cdn.shanzei.top/20200713102805.png)

### map

```go
声明和初始化
users1 := make(map[string]user)
// Add key/value pairs to the map.
users1["Roy"] = user{"Rob", "Roy"}
users1["Ford"] = user{"Henry", "Ford"}
users1["Mouse"] = user{"Mickey", "Mouse"}
users1["Jackson"] = user{"Michael", "Jackson"}

字面量初始化
users2 := map[string]user{
	"Roy":     {"Rob", "Roy"},
	"Ford":    {"Henry", "Ford"},
	"Mouse":   {"Mickey", "Mouse"},
	"Jackson": {"Michael", "Jackson"},
}

删除元素
delete(users2, "Roy")

查找元素
u1, found1 := users2["Roy"]
```

