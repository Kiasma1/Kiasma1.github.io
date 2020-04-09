# 2. 打包与工具链


进一步介绍如何把代码组织成包，以及如何操作这些包

<!--more-->

# Ch03、打包与工具链

## 3.1 包

所有 Go 语言的程序都会组织成若干组文件，每组文件被称为一个包。这样每个包的代码都
可以作为很小的复用单元，被其他项目引用。让我们看看标准库中的 http 包是怎么利用包的特
性组织功能的：

```
net/http/
	cgi/
	cookiejar/
		testdata/
	fcgi/
	httptest/
	httputil/
	pprof/
	testdata/
```

这些目录包括一系列以.go 为扩展名的相关文件。这些目录将实现 HTTP 服务器、客户端、
测试工具和性能调试工具的相关代码拆分成功能清晰的、小的代码单元。以 cookiejar 包为例，
这个包里包含与存储和获取网页会话上的 cookie 相关的代码。每个包都可以单独导入和使用，以
便开发者可以根据自己的需要导入特定功能。例如，如果要实现 HTTP 客户端，只需要导入 http
包就可以。

### 3.1.1 包名惯例

给包命名的惯例是使用包所在目录的名字。给包及其目录命名时，应该使用简洁、清晰且全小写的名字，这有利于开发时频繁输入包名。例如，net/http 包下面的包，如 cgi、httputil 和 pprof，名字都很简洁。

### 3.1.2 main包

在 Go 语言里，命名为 main 的包具有特殊的含义。Go 语言的编译程序会试图把这种名字的包编译为二进制可执行文件。所有用 Go 语言编译的可执行程序都必须有一个名叫 main 的包。

当编译器发现某个包的名字为 main 时，它一定也会发现名为 main()的函数，否则不会创建可执行文件。main()函数是程序的入口，所以，如果没有这个函数，程序就没有办法开始执行。程序编译时，会使用声明 main 包的代码所在的目录的目录名作为二进制可执行文件的文件名。

## 3.2 导入

```go
import (
	"fmt"
    "strings"
)
```

编译器会使用 Go 环境变量设置的路径，通过引入的相对路径来查找磁盘上的包。标准库中的包会在安装 Go 的位置找到。Go 开发者创建的包会在 GOPATH 环境变量指定的目录里查找。
GOPATH 指定的这些目录就是开发者的个人工作空间。

举个例子。如果 Go 安装在`/usr/local/go`，并且环境变量 GOPATH 设置为`/home/myproject:/home/mylibraries`，编译器就会按照下面的顺序查找 net/http 包：

```
/usr/local/go/src/net/http
/home/myproject/src/net/http
/home/mylibraries/src/net/http
```

一旦编译器找到一个满足 import 语句的包，就停止进一步查找。有一件重要的事需要记住，编译器会首先查找 Go 的安装目录，然后才会按顺序查找 GOPATH 变量里列出的目录。
如果编译器查遍 GOPATH 也没有找到要导入的包，那么在试图对程序执行 run 或者 build
的时候就会出错。本章后面会介绍如何通过 go get 命令来修正这种错误

### 3.2.1 远程导入

例如：

` import "github.com/spf13/viper" `

用导入路径编译程序时，go build 命令会使用GOPATH 的设置，在磁盘上搜索这个包。事实上，
这个导入路径代表一个 URL，指向 GitHub 上的代码库。如果路径包含 URL，可以使用 Go 工具链从DVCS 获取包，并把包的源代码保存在 GOPATH 指向的路径里与 URL 匹配的目录里。这个获取过程使用 go get 命令完成。go get 将获取任意指定的 URL 的包，或者一个已经导入的包所依赖的其他包。由于go get 的这种递归特性，这个命令会扫描某个包的源码树，获取能找到的所有依赖包。

### 3.2.2 命名导入

重名的包可以通过命名导入来导入。命名导入是指，在 import 语句给出的包路径的左侧定义一个名字，将导入的包命名为新名字。

```go
package main

import (
	"fmt"
    myfmt "mylib/fmt"
)

func main() {
    fmt.Println()
    myfmt.Println()
}
```

**当你导入了一个不在代码里使用的包时，Go 编译器会编译失败，并输出一个错误。**

**用户可能需要导入一个包，但是不需要引用这个包的标识符。在这种情况，可以使用
空白标识符_来重命名这个导入。**

## 3.3 函数init

每个包可以包含任意多个 init 函数，这些函数都会在程序执行开始的时候被调用。所有被
编译器发现的 init 函数都会安排在 main 函数之前执行。init 函数用在设置包、初始化变量
或者其他要在程序运行前优先完成的引导工作。

如果程序导入了有init的这个包，在运行之前就会调用init。

## 3.5 Go开发工具

### 3.5.1  go vet

vet 命令会帮开发人
员检测代码的常见错误。让我们看看 vet 捕获哪些类型的错误。

- Printf 类函数调用时，类型匹配错误的参数。
- 定义常用的方法时，方法签名的错误。
- 错误的结构标签。
- 没有指定字段名的结构字面量。

### 3.5.2 Go代码格式化  go  fmt

fmt 工具会将开发人员的代码布局成和 Go 源代码类似的风格。

### 3.5.3 Go语言的文档

1. 从命令行获取文档

` go doc tar`

2. 浏览文档

` godoc -http=:6060`

自己的包的文档也可以生成

```
// Retrieve 连接到配置库，收集各种链接设置、用户名和密码。这个函数在成功时
// 返回 config 结构，否则返回一个错误。
func Retrieve() (config, error) {
// ..．省略
}
```

如果想给包写一段文字量比较大的文档，可以在工
程里包含一个叫作 doc.go 的文件，使用同样的包名，并把包的介绍使用注释加在包名声明之前。

```go
/*
包 usb 提供了用于调用 USB 设备的类型和函数。想要与 USB 设备创建一个新链接，使用 NewConnection
...
*/
package usb
```


这段关于包的文档会显示在所有类型和函数文档之前。

## 3.6 与其他 Go 开发者合作
现代开发者不会一个人单打独斗，而 Go 工具也认可这个趋势，并为合作提供了支持。多亏
了 go 工具链，包的概念没有被限制在本地开发环境中，而是做了扩展，从而支持现代合作方式。让我们看看在分布式开发环境里，想要良好合作，需要遵守的一些惯例。

**以分享为目的创建代码库**
开发人员一旦写了些非常棒的 Go 代码，就会很想把这些代码与 Go 社区的其他人分享。这其实很容易，只需要执行下面的步骤就可以。

1．包应该在代码库的根目录中

使用 go get 的时候，开发人员指定了要导入包的全路径。这意味着在创建想要分享的代码库的时候，包名应该就是代码库的名字，而且包的源代码应该位于代码库目录结构的根目录。

Go 语言新手常犯的一个错误是，在公用代码库里创建一个名为 code 或者 src 的目录。如果这么做，会让导入公用库的语句变得很长。为了避免过长的语句，只需要把包的源文件放在公
用代码库的根目录就好。

2．包可以非常小

与其他语言相比，Go 语言的包一般相对较小。不要在意包只支持几个 API，或者只完成一项任务。在 Go 语言里，这样的包很常见，而且很受欢迎。

3．对代码执行 go fmt

和其他开源代码库一样，人们在试用代码前会通过源代码来判断代码的质量。开发人员需要在签入代码前执行 go fmt，这样能让自己的代码可读性更好，而且不会由于一些字符的干扰（如制表符），在不同人的计算机上代码显示的效果不一样。

4．给代码写文档

Go 开发者用godoc 来阅读文档，并且会用 http://godoc.org 这个网站来阅读开源包的文档。如果按照go doc 的最佳实践来给代码写文档，包的文档在本地和线上都会很好看，更容易被别人发现。

## 3.7 依赖管理

### 3.7.1 第三方依赖

像 godep 和 vender 这种社区工具已经使用第三方（verdoring）导入路径重写这种特性解决了
依赖问题。其思想是把所有的依赖包复制到工程代码库中的目录里，然后使用工程内部的依赖包
所在目录来重写所有的导入路径。

``` 
$GOPATH/src/github.com/ardanstudios/myproject
|-- Godeps
| 	|-- Godeps.json
| 	|-- Readme
| 	|-- _workspace
| 		|-- src
| 			|-- bitbucket.org
| 			|-- ww
| 			|	 |-- goautoneg
| 			|		 |-- Makefile
| 			|		 |-- README.txt
| 			|		 |-- autoneg.go
| 			|		 |-- autoneg_test.go
| 			|-- github.com
| 				|-- beorn7
| 					|-- perks
| 						|-- README.md
| 						|-- quantile
| 							|-- bench_test.go
| 							|-- example_test.go
| 							|-- exampledata.txt
| 							|-- stream.go
|
|-- examples
|-- model
|-- README.md
|-- main.go
```

可以看到 godep 创建了一个叫作 Godeps 的目录。由这个工具管理的依赖的源代码被放在一个叫作_ workspace/src 的目录里。

接下来，如果看一下在 main.go 里声明这些依赖的 import 语句就能发现需要改动的地方。

```
在路径重写之前
01 package main
02
03 import (
04 "bitbucket.org/ww/goautoneg"
05 "github.com/beorn7/perks"
06 )
在路径重写之后
01 package main
02
03 import (
04 "github.ardanstudios.com/myproject/Godeps/_workspace/src/
bitbucket.org/ww/goautoneg"
05 "github.ardanstudios.com/myproject/Godeps/_workspace/src/
github.com/beorn7/perks"
06 )
```

在路径重写之前，import 语句使用的是包的正常路径。包对应的代码存放在 GOPATH 所指定的磁盘目录里。在依赖管理之后，导入路径需要重写成工程内部依赖包的路径。可以看到这些导入路径非常长，不易于使用。

引入依赖管理将所有构建时依赖的源代码都导入到一个单独的工程代码库里，可以更容易地重新构建工程。使用导入路径重写管理依赖包的另外一个好处是这个工程依旧支持通过 go get 获取代码库。当获取这个工程的代码库时，go get 可以找到每个包，并将其保存到工程里正确的目录中。

### 3.7.2 对gb的介绍

gb 是一个由 Go 社区成员开发的全新的构建工具。

gb 既不包装 Go 工具链，也不使用 GOPATH。gb 基于工程将 Go 工具链工作空间的元信息做替换。这种依赖管理的方法不需要重写工程内代码的导入路径。而且导入路径依旧通过 go get 和 GOPATH 工作空间来管理。

```
/home/bill/devel/myproject ($PROJECT)
|-- src
| 	|-- cmd
| 	| 	|-- myproject
| 	| 	|	 |-- main.go
| 	|-- examples
| 	|-- model
| 	|-- README.md
|-- vendor
	|-- src
		|-- bitbucket.org
		|	 |-- ww
		|	 	|-- goautoneg
		|		|-- Makefile
		|		|-- README.txt
		|		|-- autoneg.go
		|		|-- autoneg_test.go
		|-- github.com
			|-- beorn7
				|-- perks
				|-- README.md
				|-- quantile
				|-- bench_test.go
		|-- example_test.go
		|-- exampledata.txt
		|-- stream.go
```

一个 gb 工程就是磁盘上一个包含 src/子目录的目录。gb 工程会区分开发人员写的代码和开发人员需要依赖的代码。

```
工程中存放开发人员写的代码的位置
$PROJECT/src/
存放第三方代码的位置
$PROJECT/vendor/src/

gb 工程的导入路径
01 package main
02
03 import (
04 "bitbucket.org/ww/goautoneg"
05 "github.com/beorn7/perks"
06 )
```

gb工具首先会在`$PROJECT/src/`目录中查找代码，如果找不到，会在`$PROJECT/vender/src/`目录里查找。与工程相关的整个源代码都会在同一个代码库里。自己写的代码在工程目录的` src/`目录中，第三方依赖代码在工程目录的` vender/src `子目录中。这样，不需要配合重写导入路径也可以完成整个构建过程，同时可以把整个工程放到磁盘的任意位置。这些特点，让 gb 成为社区里解决可重复构建的流行工具。

还需要提一点：gb 工程与 Go 官方工具链（包括 go get）并不兼容。因为 gb 不需要设置
GOPATH，而 Go 工具链无法理解 gb 工程的目录结构，所以无法用 Go 工具链构建、测试或者获
取代码。构建（如代码清单 3-16 所示）和测试 gb 工程需要先进入$PROJECT 目录，并使用 gb
工具。

`gb build all`

很多 Go 工具支持的特性，gb 都提供对应的特性。gb 还提供了插件系统，可以让社区扩展
支持的功能。其中一个插件叫作 vender。这个插件可以方便地管理`$PROJECT/vender/src/`
目录里的依赖关系，而这个功能 Go 工具链至今没有提供。
