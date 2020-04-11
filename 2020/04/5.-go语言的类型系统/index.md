# 5. Go语言的类型系统


Go 语言是一种静态类型的编程语言。这意味着，编译器需要在编译时知晓程序里每个值的类型。如果提前知道类型信息，编译器就可以确保程序合理地使用值。这有助于减少潜在的内存异常和 bug，并且使编译器有机会对代码进行一些性能优化，提高执行效率。

<!--more-->

值的类型给编译器提供两部分信息：

- 第一部分，需要分配多少内存给这个值（即值的规模）；

- 第二部分，这段内存表示什么。对于许多内置类型的情况来说，规模和表示是类型名的一部分。

int64 类型的值需要 8 字节（64 位），表示一个整数值；float32 类型的值需要 4 字节（32 位），表示一个 IEEE-754 定义的二进制浮点数；bool 类型的值需要 1 字节（8 位），表示布尔值 true和 false。

有些类型的内部表示与编译代码的机器的体系结构有关。例如，根据编译所在的机器的体系结构，一个 int 值的大小可能是 8 字节（64 位），也可能是 4 字节（32 位）。还有一些与体系结构相关的类型，如 Go 语言里的所有引用类型。好在创建和使用这些类型的值的时候，不需要了解这些与体系结构相关的信息。但是，如果编译器不知道这些信息，就无法阻止用户做一些导致程序受损甚至机器故障的事情。

# Ch05 Go语言的类型系统

## 5.1 用户定义的类型

```go
//定义一个结构类型
type user struct {
    name string
    email string
    ext int
    privileged bool
}

//用var声明一个结构类型
var bill user

//用字面量声明一个结构类型
lisa := user {
    name:  "Lisa",
    email: "lisa@email.com",
    ext:   123,
    privileged: true,
}

//用字面量声明一个结构类型（简化）
lisa := user{"Lisa", "lisa@email.com", 123, true}
```

复合类型

```go
//定义类型
type admin struct {
    person user
    level  string
}

//声明
fred := admin{
    person: user {
        name:  "Lisa",
        email: "lisa@email.com",
        ext:   123,
        privileged: true,
    },
    level: "super",
}

//声明（简化）
fred := admin{
    user{"Lisa", "lisa@email.com", 123, true},
    "super",
}
```

```go
package main

type Duration int64

func main() {
    var dur Duration
    due = int64(1000)
}

prog.go:7: cannot use int64(1000) (type int64) as type Duration in assignment
```
编译器很清楚这个程序的问题：类型 int64 的值不能作为类型 Duration 的值来用。换句话说，虽然 int64 类型是基础类型，Duration 类型依然是一个独立的类型。两种不同类型的值即便互相兼容，也不能互相赋值。编译器不会对不同类型的值做隐式转换。

## 5.2 方法

```go
//定义user
type user struct {
    name  string
    email string
}

//接收值
func (u user) notify() {
    fmt.Println(u.name, u.email)
}

//接收指针
func (u *user) notify() {
    fmt.Println(u.name, u.email)
}

//调用接受值的
bill := user{"Bill", "bill@email.com"}
bill.notify()

//调用接收值的（指针也可以调用）
lisa := &user{"Lisa", "lisa@email.com"}
lisa.notify() //背后 (*lisa).notify()

//调用接收指针的（值也可以调用）
bill := user{"Bill", "bill@email.com"}
bill.notify()//背后 (&user).notify()

//调用接收指针的
lisa := &user{"Lisa", "lisa@email.com"}
lisa.notify()
```

## 5.3 类型的本质

### 5.3.1 内置类型

内置类型是由语言提供的一组类型。我们已经见过这些类型，分别是数值类型、字符串类型和布尔类型。这些类型本质上是原始的类型。因此，当对这些值进行增加或者删除的时候，会创建一个新值。基于这个结论，当把这些类型的值传递给方法或者函数时，应该传递一个对应值的副本。

```go
func isShellSpecialVar(c uint8) bool {
    switch c {
    case '*', '#', '$', '@', '!', '?', '0', '1', '2', '3', '4', '5', '6', '7', '8', '9':
        return true
    }
    return false
}
```

### 5.3.2 引用类型

**Go 语言里的引用类型有如下几个：切片、映射、通道、接口和函数类型。**

当声明上述类型的变量时，创建的变量被称作标头（header）值。从技术细节上说，字符串也是一种引用类型。每个引用类型创建的标头值是包含一个指向底层数据结构的指针。每个引用类型还包含一组独特的字段，用于管理底层数据结构。因为标头值是为复制而设计的，所以永远不需要共享一个引用类型的值。**标头值里包含一个指针，因此通过复制来传递一个引用类型的值的副本，本质上就是在共享底层数据结构。**

```go
type IP []byte

func (ip IP) MarshalText() ([]byte, error) {
    if len(ip) == 0 {
        return []byte(""), nil
    }
    if len(ip) != IPv4len && len(ip) != IPv6len {
        return nil, errors.New("invalid IP address")
    }
    return []byte(ip.String), nil
}
```

引用类型的值在其他方面像原始的数据类型的值一样对待。

### 5.3.3 结构类型

结构类型可以用来描述一组数据值，这组值的本质即可以是原始的，也可以是非原始的。

#### 1. 原始的

```go
type Time struct {
    // sec 给出自公元 1 年 1 月 1 日 00:00:00
    // 开始的秒数
    sec int64

    // nsec 指定了一秒内的纳秒偏移，
    // 这个值是非零值，
    // 必须在[0, 999999999]范围内
    nsec int32

    // loc 指定了一个 Location，
    // 用于决定该时间对应的当地的分、小时、天和年的值
    // 只有 Time 的零值，其 loc 的值是 nil
    // 这种情况下，认为处于 UTC 时区
    loc *Location
}

func Now() Time {
    sec, nsec := now()
    return Time{sec + unixToInternal, nsec, Local}
}

func (t Time) Add(d Duration) Time {
    t.sec += int64(d / 1e9)
    nsec := int32(t.nsec) + int32(d%1e9)
    if nsec >= 1e9 {
        t.sec++
        nsec -= 1e9
    } else if nsec < 0 {
        t.sec--
        nsec += 1e9
    }
    t.nsec = nsec
    return t
}

这个方法使用值接收者，并返回了一个新的 Time 值。该方法操作的是调用者传入的 Time 值的副本，并且给调用者返回了一个方法内的 Time 值的副本。至于是使用返回的值替换原来的 Time 值，还是创建一个新的 Time 变量来保存结果，是由调用者决定的
事情。
```

#### 2. 非原始的

大多数情况下，结构类型的本质并不是原始的，而是非原始的。这种情况下，对这个类型的值做增加或者删除的操作应该更改值本身。当需要修改值本身时，在程序中其他地方，需要使用指针来共享这个值。

```go
// File 表示一个打开的文件描述符
type File struct {
    *file
}

// file 是*File 的实际表示
// 额外的一层结构保证没有哪个 os 的客户端
// 能够覆盖这些数据。如果覆盖这些数据，
// 可能在变量终结时关闭错误的文件描述符
type file struct {
    fd int
    name string
    dirinfo *dirInfo // 除了目录结构，此字段为 nil
    nepipe int32 // Write 操作时遇到连续 EPIPE 的次数
}

func Open(name string) (file *File, err error) {
    return OpenFile(name, O_RDONLY, 0)
}

func (f *File) Chdir() error {
    if f == nil {
        return ErrInvalid
    }
    if e := syscall.Fchdir(f.fd); e != nil {
        return &PathError{"chdir", f.name, e}
    }
    return nil
}
```

Open 创建了 File 类型的值，并返回指向这个值的指针。如果一个创建用的工厂函数返回了一个指针，就表示这个被返回的值的本质是非原始的。

Chdir 方法展示了，即使没有修改接收者的值，依然是用指针接收者来声明的。因为 File 类型的值具备非原始的本质，所以总是应该被共享，而不是被复制。

**是使用值接收者还是指针接收者，不应该由该方法是否修改了接收到的值来决定。这个决策应该基于该类型的本质。这条规则的一个例外是，需要让类型值符合某个接口的时候，即便类型的本质是非原始本质的，也可以选择使用值接收者声明方法。这样做完全符合接口值调用方法的机制。**

## 5.4 接口

**多态是指代码可以根据类型的具体实现采取不同行为的能力。** 如果一个类型实现了某个接口，所有使用这个接口的地方，都可以支持这种类型的值。标准库里有很好的例子，如 io 包里实现的流式处理接口。io 包提供了一组构造得非常好的接口和函数，来让代码轻松支持流式数据处理。只要实现两个接口，就能利用整个 io 包背后的所有强大能力。

### 5.4.1 标准库

```go
package main

import (
    "fmt"
    "io"
    "net/http"
    "os"
)

func init() {
    if len(os.Args) != 2 {
        fmt.Println("Usage: ./example2 <url>")
        os.Exit(-1)
    }
}

func main(){
    // 从 Web 服务器得到响应
    r, err := http.Get(os.Args[1])
    if err != nil {
        fmt.Prinln(err)
        return
    }

    // 从 Body 复制到 Stdout
    io.Copy(os.Stdout, r.Body)
    if err := r.Body.Close(); err != nil {
        fmt.Println(err)
    }
}
```

io.Copy 函数的第二个参数，接受一个 io.Reader 接口类型的值，这个值表示数据流入的源。Body 字段实现了 io.Reader接口，因此我们可以将 Body 字段传入 io.Copy，使用 Web 服务器的返回内容作为源。

io.Copy 的第一个参数是复制到的目标，这个参数必须是一个实现了 io.Writer 接口的值。对于这个目标，我们传入了 os 包里的一个特殊值 Stdout。这个接口值表示标准输出设备，并且已经实现了 io.Writer 接口。当我们将 Body 和 Stdout 这两个值传给 io.Copy 函数后，这个函数会把服务器的数据分成小段，源源不断地传给终端窗口，直到最后一个片段读取并写入终端，io.Copy 函数才返回。

```go
func main() {
    var b bytes.Buffer

    // 将字符串写入 Buffer
    b.Write([]byte("Hello"))

    // 使用 Fprintf 将字符串拼接到 Buffer
    fmt.Fprintf(&b, "World!")

    // 将 Buffer 的内容写到 Stdout
    io.Copy(os.Stdout, &b)
}
```

创建了一个 `bytes` 包里的 `Buffer` 类型的变量 `b`，用于缓冲数据。之后使用 `Write` 方法将字符串 `Hello` 写入这个缓冲区 `b`。调用 `fmt` 包里的 `Fprintf` 函数，将第二个字符串追加到缓冲区 `b` 里。

`fmt.Fprintf` 函数接受一个 `io.Writer` 类型的接口值作为其第一个参数。由于`bytes.Buffer` 类型的指针实现了 `io.Writer` 接口，所以可以将缓存 `b` 传入 `fmt.Fprintf` 函数，并执行追加操作。

再次使用 `io.Copy` 函数，将字符写到终端窗口。由于 `bytes.Buffer` 类型的指针也实现了 `io.Reader` 接口，`io.Copy` 函数可以用于在终端窗口显示缓冲区 `b` 的内容。

### 5.4.2 实现

接口是用来定义行为的类型。这些被定义的行为不由接口直接实现，而是通过方法由用户定义的类型实现。如果用户定义的类型实现了某个接口类型声明的一组方法，那么这个用户定义的类型的值就可以赋给这个接口类型的值。这个赋值会把用户定义的类型的值存入接口类型
的值。

在这个关系里，用户定义的类型通常叫作**实体类型**，原因是如果离开内部存储的用户定义的类型的值的实现，接口值并没有具体的行为。

![](http://cdn.shanzei.top/20200411224246.png)

![](http://cdn.shanzei.top/20200411224332.png)

**简单理解：当user赋值给接口n时（一般来说，函数调用参数也是赋值），user会进入iTable中，如果user方法集中没有接口需要实现的方法，那么加入失败。**

### 5.4.3 方法集

方法集定义了接口的接受规则。

方法集定义了一组关联到给定类型的值或者指针的方法。定义方法时使用的接收者的类型决定了这个方法是关联到值，还是关联到指针，还是两个都关联。

![](http://cdn.shanzei.top/20200411230343.png)

```go
package main

import (
    "fmt"
)

type notifier interface {
    notify()
}

type user struct {
    name string
    email string
}

func (u *user) notify() {
    fmt.Println(u.name, u.email)
}

func main() {
    u := user{"Bill", "bill@email.com"}

    sendNotification(&u)

}

func sendNotification(n notifier) {
        n.notify()
}
```

### 5.4.4 多态

```go
func main(){
    bill := user{"Bill", "bill@email.com"}
    sendNotification(&bill)
    lisa := admin{"Lisa", "lisa@emali.com"}
    sendNotification(&lisa)
}

func sendNotification(n notifier){
    n.notify
}
```

## 5.5 嵌入类型（类似继承）

Go 语言允许用户扩展或者修改已有类型的行为。这个功能对代码复用很重要，在修改已有类型以符合新类型的时候也很重要。这个功能是通过**嵌入类型（type embedding）**完成的。嵌入类型是将已有的类型直接声明在新的结构类型里。被嵌入的类型被称为新的外部类型的内部类型。

**通过嵌入类型，与内部类型相关的标识符会提升到外部类型上。这些被提升的标识符就像直接声明在外部类型里的标识符一样，也是外部类型的一部分。这样外部类型就组合了内部类型包含的所有属性，并且可以添加新的字段和方法。外部类型也可以通过声明与内部类型标识符同名的标识符来覆盖内部标识符的字段或者方法。这就是扩展或者修改已有类型的方法。**

```go
// 这个示例程序展示如何将一个类型嵌入另一个类型，以及
// 内部类型和外部类型之间的关系
package main

import (
    "fmt"
)

// user 在程序里定义一个用户类型
type user struct {
    name string
    email string
}

// notify 实现了一个可以通过 user 类型值的指针
// 调用的方法
func (u *user) notify() {
    fmt.Printf("Sending user email to %s<%s>\n",
    u.name,
    u.email)
}

// admin 代表一个拥有权限的管理员用户
type admin struct {
    user // 嵌入类型
    level string
}

// main 是应用程序的入口
func main() {
    // 创建一个 admin 用户
    ad := admin{
        user: user{
            name: "john smith",
            email: "john@yahoo.com",
        },
        level: "super",
    }

// 我们可以直接访问内部类型的方法
    ad.user.notify()

// 内部类型的方法也被提升到外部类型
    ad.notify()
}
```

**如果外部类型实现了 notify 方法，内部类型的实现就不会被提升。不过内部类型的值一直存在，因此还可以通过直接访问内部类型的值，来调用没有被提升的内部类型实现的方法。（类似重载）**

## 5.6 公开或未公开的标识符

Go 语言支持从包里公开或者隐藏标识符。通过这个功能，让用户能按照自己的规则控制标识符的可见性。

**当一个标识符的名字以小写字母开头时，这个标识符就是未公开的，即包外的代码不可见。如果一个标识符以大写字母开头，这个标识符就是公开的，即被包外的代码可见。**

**即便内部类型是未公开的，内部类型里声明的字段依旧是公开的。既然内部类型的标识符提升到了外部类型，这些公开的字段也可以通过外部类型的字段的值来访问。**

可以这么做的原因有两点

- **第一，公开或者未公开的标识符，不是一个值。**

- **第二，短变量声明操作符，有能力捕获引用的类型，并创建一个未公开的类型的变量。永远不能显式创建一个未公开的类型的变量，不过短变量声明操作符可以这么做。**

## 5.7 小结

- 使用关键字 struct 或者通过指定已经存在的类型，可以声明用户定义的类型。
- **方法提供了一种给用户定义的类型增加行为的方式。**
- 设计类型时需要确认类型的本质是原始的，还是非原始的。
- 接口是声明了一组行为并支持多态的类型。
- **嵌入类型提供了扩展类型的能力，而无需使用继承。**
- 标识符要么是从包里公开的，要么是在包里未公开的。
