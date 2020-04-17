# 8. 标准库


Go 标准库是一组核心包，用来扩展和增强语言的能力。这些包为语言增加了大量不同的类型。开发人员可以直接使用这些类型，而不用再写自己的包或者去下载其他人发布的第三方包。

<!--more-->

# Ch08 标准库

## 8.1 文档与源代码

Go语言团队在网站上维护了一个文档，参见 http://golang.org/pkg/。golang 网站的 pkg 页面提供了每个包的 godoc 文档。

作为 Go 发布包的一部分，标准库的源代码是经过预编译的。这些预编译后的文件，称作**归档文件**（archive file），可以 在 `$GOROOT/pkg  文件夹中找到已经安装的各目标平台和操作系统的归档文件。可以看到扩展名是.a 的文件，这些就是归档文件。

Go 工具链知道什么时候可以使用已有的.a 文件，什么时候需要从机器上的源代码重新构建。

## 8.2 记录日记

在 UNIX 里，日志有很长的历史。这些积累下来的经验都体现在 log 包的设计里。传统的CLI（命令行界面）程序直接将输出写到名为 `stdout` 的设备上。所有的操作系统上都有这种设备，这种设备的默认目的地是标准文本输出。默认设置下，终端会显示这些写到 `stdout` 设备上的文本。这种单个目的地的输出用起来很方便，不过你总会碰到需要同时输出程序信息和输出执行细节的情况。这些执行细节被称作日志。当想要记录日志时，你希望能写到不同的目的地，这样就不会将程序的输出和日志混在一起了。

为了解决这个问题，UNIX 架构上增加了一个叫作 `stderr` 的设备。这个设备被创建为日志的默认目的地。这样开发人员就能将程序的输出和日志分离开来。如果想在程序运行时同时看到程序输出和日志，可以将终端配置为同时显示写到 `stdout` 和 `stderr` 的信息。不过，如果用户的程序只记录日志，没有程序输出，更常用的方式是将一般的日志信息写到 `stdout`，将错误或者警告信息写到 `stderr`。

### 8.2.1 log包

```
//跟踪日志的样例
TRACE: 2009/11/10 23:00:00.000000 /tmpfs/gosandbox-/prog.go:14: message
```

可以看到一个由 log 包产生的日志项。这个日志项包含前缀、日期时间戳、该日志具体是由哪个源文件记录的、源文件记录日志所在行，最后是日志消息。让我们看一下如何配置 log 包来输出这样的日志项

```go
package main

import "log"

func init() {
    log.SetPrefix("TRACE: ")
    log.SetFlags(log.Ldate | log.Lmicroseconds | log.Llongfile)
}

func main() {
    // Println 写到标准日志记录器
    log.Println("message")

    // Fatalln 在调用 Println()之后会接着调用 os.Exit(1)
    log.Fatalln("fatal message")

    // Panicln 在调用 Println()之后会接着调用 panic()
    log.Panicln("panic message")
}
```

通常程序会在这个 `init()` 函数里配置日志参数，这样程序一开始就能使用 log 包进行正确的输出。在这段程序的第 6 行，设置了一个字符串，作为每个日志项的前缀。这个字符串应该是能让用户从一般的程序输出中分辨出日志的字符串。传统上这个字符串的字符会全部大写。

有几个和 log 包相关联的标志，这些标志用来控制可以写到每个日志项的其他信息。代码

```go
//展示了目前包含的所有标志。
const (
    // 将下面的位使用或运算符连接在一起，可以控制要输出的信息。没有
    // 办法控制这些信息出现的顺序（下面会给出顺序）或者打印的格式
    // （格式在注释里描述）。这些项后面会有一个冒号：
    // 2009/01/23 01:23:23.123123 /a/b/c/d.go:23: message
    // 日期: 2009/01/23
    Ldate = 1 << iota
    // 时间: 01:23:23
    Ltime
    // 毫秒级时间: 01:23:23.123123。该设置会覆盖 Ltime 标志
    Lmicroseconds
    // 完整路径的文件名和行号: /a/b/c/d.go:23
    Llongfile
    // 最终的文件名元素和行号: d.go:23
    // 覆盖 Llongfile
    Lshortfile
    // 标准日志记录器的初始值
    LstdFlags = Ldate | Ltime
)
```

这里的 `Ldate` 使用的是一种特殊的语法来声明的。

```go
Ldate = 1 << iota
```

关键字 `iota` 在常量声明区里有特殊的作用。这个关键字让编译器为每个常量复制相同的表达式，直到声明区结束，或者遇到一个新的赋值语句。关键字 `iota` 的另一个功能是，`iota` 的初始值为 0，之后 `iota` 的值在每次处理为常量后，都会自增 1。

```go
const (
Ldate = 1 << iota // 1 << 0 = 000000001 = 1
Ltime // 1 << 1 = 000000010 = 2
Lmicroseconds // 1 << 2 = 000000100 = 4
Llongfile // 1 << 3 = 000001000 = 8
Lshortfile // 1 << 4 = 000010000 = 16
...
)
```

操作符<<对左边的操作数执行按位左移操作。在每个常量声明时，都将 1 按位左移 iota 个位置。最终的效果使为每个常量赋予一个独立位置的位，这正好是标志希望的工作方式。

这里 `LstdFlags` 的赋值打破了 `iota` 常数链，而且我们可以得出该常量的值是`000000011` 即`Ldate`和`Ltime`标志位都被标一,即将`Ldate`和`Ltime`一起输出。

如何使用 3 个函数 `Println`、`Fatalln` 和 `Panicln` 来写日志消息。这些函数也有可以格式化消息的版本，只需要用 f 替换结尾的 ln。`Fatal` 系列函数用来写日志消息，然后使用 `os.Exit(1)` 终止程序。`Panic` 系列函数用来写日志消息，然后触发一个 `panic`。除非程序执行 `recover` 函数，否则会导致程序打印调用栈后终止。`Print` 系列函数是写日志消息的标准方法。

log 包有一个很方便的地方就是，这些日志记录器是多 `goroutine` 安全的。这意味着在多个 `goroutine` 可以同时调用来自同一个日志记录器的这些函数，而不 会有彼此间的写冲突。标准日志记录器具有这一性质，用户定制的日志记录器也应该满足这一性质。

### 8.2.2 定制的日志记录器

要想创建一个定制的日志记录器，需要创建一个 Logger 类型值。可以给每个日志记录器配置一个单独的目的地，并独立设置其前缀和标志。

```go
// This sample program demonstrates how to create customized loggers.
package main

import (
	"io"
	"io/ioutil"
	"log"
	"os"
)

var (
	Trace   *log.Logger // Just about anything
	Info    *log.Logger // Important information
	Warning *log.Logger // Be concerned
	Error   *log.Logger // Critical problem
)

func init() {
	file, err := os.OpenFile("errors.txt",
		os.O_CREATE|os.O_WRONLY|os.O_APPEND, 0666)
	if err != nil {
		log.Fatalln("Failed to open error log file:", err)
	}

	Trace = log.New(ioutil.Discard,
		"TRACE: ",
		log.Ldate|log.Ltime|log.Lshortfile)

	Info = log.New(os.Stdout,
		"INFO: ",
		log.Ldate|log.Ltime|log.Lshortfile)

	Warning = log.New(os.Stdout,
		"WARNING: ",
		log.Ldate|log.Ltime|log.Lshortfile)

	Error = log.New(io.MultiWriter(file, os.Stderr),
		"ERROR: ",
		log.Ldate|log.Ltime|log.Lshortfile)
}

func main() {
	Trace.Println("I have something standard to say")
	Info.Println("Special Information")
	Warning.Println("There is something you need to know about")
	Error.Println("Something has failed")
}
```

这段程序创建了 4 种不同的 Logger 类型的指针变量，分别命名为 Trace、Info、Warning 和 Error。每个变量使用不同的配置，用来表示不同的重要程度。

```go
// New 创建一个新的 Logger。out 参数设置日志数据将被写入的目的地
// 参数 prefix 会在生成的每行日志的最开始出现
// 参数 flag 定义日志记录包含哪些属性
func New(out io.Writer, prefix string, flag int) *Logger {
return &Logger{out: out, prefix: prefix, flag: flag}
}
```

```go
// devNull 是一个用 int 作为基础类型的类型
type devNull int
// Discard 是一个 io.Writer，所有的 Write 调用都不会有动作，但是会成功返回
var Discard io.Writer = devNull(0)
// io.Writer 接口的实现
func (devNull) Write(p []byte) (int, error) {
 return len(p), nil
}
```

展示了 `Discard` 变量的声明以及相关的实现。`Discard` 变量的类型被声明
为 `io.Writer` 接口类型，并被给定了一个 `devNull` 类型的值 0。基于 `devNull` 类型实现的 `Write` 方法，会忽略所有写入这一变量的数据。当某个等级的日志不重要时，使用 `Discard` 变量可以禁用这个等级的日志。

`io.MultiWriter(file, os.Stderr)`这个函数调用会返回一个 `io.Writer` 接口类型值，这个值包含之前打开的文件file，以及 stderr。`MultiWriter` 函数是一个变参函数，可以接受任意个实现了 `io.Writer` 接口的值。这个函数会返回一个`io.Writer` 值，这个值会把所有传入的 `io.Writer` 的值绑在一起。当对这个返回值进行写入时，会向所有绑在一起的 `io.Writer` 值做写入。这让类似log.New 这样的函数可以同时向多个 `Writer` 做输出。现在，当我们使用Error 记录器记录日志时，输出会同时写到文件和stderr。

每个记录器变量都包含一组方法，这组方法与 log 包里实现的
那组函数完全一致, Logger 类型实现的所有方法。

```go
func (l *Logger) Fatal(v ...interface{})
func (l *Logger) Fatalf(format string, v ...interface{})
func (l *Logger) Fatalln(v ...interface{})
func (l *Logger) Flags() int
func (l *Logger) Output(calldepth int, s string) error
func (l *Logger) Panic(v ...interface{})
func (l *Logger) Panicf(format string, v ...interface{})
func (l *Logger) Panicln(v ...interface{})
func (l *Logger) Prefix() string
func (l *Logger) Print(v ...interface{})
func (l *Logger) Printf(format string, v ...interface{})
func (l *Logger) Println(v ...interface{})
func (l *Logger) SetFlags(flag int)
func (l *Logger) SetPrefix(prefix string)
```

### 8.2.3 结论

log 包的实现，是基于对记录日志这个需求长时间的实践和积累而形成的。将输出写到 stdout，将日志记录到 stderr，是很多基于命令行界面（CLI）的程序的惯常使用的方法。不过如果你的程序只输出日志，那么使用 stdout 、 stderr 和文件来记录日志是很好的做法。

## 8.3 编码/解码

### 8.3.1 解码JSON

使用 json 包的 `NewDecoder` 函数以及 `Decode` 方法进行解码

`gResponse` 和 `gResult` 的类型声明，你会注意到每个字段最后使用单引号声明了一个字符串。**这些字符串被称作标签（tag），是提供每个字段的元信息的一种机制，将 JSON 文档和结构类型里的字段一一映射起来。如果不存在标签，编码和解码过程会试图以大小写无关的方式，直接使用字段的名字进行匹配。如果无法匹配，对应的结构类型里的字段就包含其零值。**

```go
// This sample program demonstrates how to decode a JSON response
// using the json package and NewDecoder function.
package main

import (
	"encoding/json"
	"fmt"
	"log"
	"net/http"
)

type (
	// gResult maps to the result document received from the search.
	gResult struct {
		GsearchResultClass string `json:"GsearchResultClass"`
		UnescapedURL       string `json:"unescapedUrl"`
		URL                string `json:"url"`
		VisibleURL         string `json:"visibleUrl"`
		CacheURL           string `json:"cacheUrl"`
		Title              string `json:"title"`
		TitleNoFormatting  string `json:"titleNoFormatting"`
		Content            string `json:"content"`
	}

	// gResponse contains the top level document.
	gResponse struct {
		ResponseData struct {
			Results []gResult `json:"results"`
		} `json:"responseData"`
	}
)

func main() {
	uri := "http://ajax.googleapis.com/ajax/services/search/web?v=1.0&rsz=8&q=golang"

	// Issue the search against Google.
	resp, err := http.Get(uri)
	if err != nil {
		log.Println("ERROR:", err)
		return
	}
	defer resp.Body.Close()

	// Decode the JSON response into our struct type.
	var gr gResponse
	err = json.NewDecoder(resp.Body).Decode(&gr)
	if err != nil {
		log.Println("ERROR:", err)
		return
	}

	fmt.Println(gr)

	// Marshal the struct type into a pretty print
	// version of the JSON document.
	pretty, err := json.MarshalIndent(gr, "", "    ")
	if err != nil {
		log.Println("ERROR:", err)
		return
	}

	fmt.Println(string(pretty))
}
```

有时，需要处理的 JSON 文档会以 `string` 的形式存在。在这种情况下，需要将 `string` 转换为 `byte 切片（[]byte）`，并使用 `json` 包的 `Unmarshal` 函数进行反序列化的处理。

```go
// This sample program demonstrates how to decode a JSON string.
package main

import (
	"encoding/json"
	"fmt"
	"log"
)

// Contact represents our JSON string.
type Contact struct {
	Name    string `json:"name"`
	Title   string `json:"title"`
	Contact struct {
		Home string `json:"home"`
		Cell string `json:"cell"`
	} `json:"contact"`
}

// JSON contains a sample string to unmarshal.
var JSON = `{
	"name": "Gopher",
	"title": "programmer",
	"contact": {
		"home": "415.333.3333",
		"cell": "415.555.5555"
	}
}`

func main() {
	// Unmarshal the JSON string into our variable.
	var c Contact
	fmt.Println(JSON)
	err := json.Unmarshal([]byte(JSON), &c)
	if err != nil {
		log.Println("ERROR:", err)
		return
	}

	fmt.Println(c)
}
```

有时，无法为 JSON 的格式声明一个结构类型，而是需要更加灵活的方式来处理 JSON 文档。在这种情况下，可以将 JSON 文档解码到一个 map 变量中。

```go
// This sample program demonstrates how to decode a JSON string.
package main

import (
	"encoding/json"
	"fmt"
	"log"
)

// JSON contains a sample string to unmarshal.
var JSON = `{
	"name": "Gopher",
	"title": "programmer",
	"contact": {
		"home": "415.333.3333",
		"cell": "415.555.5555"
	}
}`

func main() {
	// Unmarshal the JSON string into our map variable.
	var c map[string]interface{}
	err := json.Unmarshal([]byte(JSON), &c)
	if err != nil {
		log.Println("ERROR:", err)
		return
	}

	fmt.Println(c)
	fmt.Println("Name:", c["name"])
	fmt.Println("Title:", c["title"])
	fmt.Println("Contact")
	fmt.Println("\tH:", c["contact"].(map[string]interface{})["home"])
	fmt.Println("\tC:", c["contact"].(map[string]interface{})["cell"])
}
```

虽然这种方法为处理 JSON 文档带来了很大的灵活性，但是却有一个小缺点。让我们看一下访问 contact 子文档的 home 字段的代码。因为每个键的值的类型都是 `interface{}`，所以必须将值转换为合适的类型，才能处理这个值。展示了如何将 contact 键的值转换为另一个键是 string 类型，值是 `interface{}` 类型的 map 类型。

### 8.3.2 编码JSON

使用json包的 `MarshalIndent` 函数进行编码。这个函数可以很方便地将 Go 语言的 map 类型的值或者结构类型的值转换为易读格式的 JSON 文档。

**序列化**（marshal）是指将数据转换为 JSON 字符串的过程。

```go
// This sample program demonstrates how to marshal a JSON string.
package main

import (
	"encoding/json"
	"fmt"
	"log"
)

func main() {
	// Create a map of key/value pairs.
	c := make(map[string]interface{})
	c["name"] = "Gopher"
	c["title"] = "programmer"
	c["contact"] = map[string]interface{}{
		"home": "415.333.3333",
		"cell": "415.555.5555",
	}

	// Marshal the map into a JSON string.
    data, err := json.MarshalIndent(c, "123", "....")
    //data, err := json.MarshalIndent(c, "", "    ")
	if err != nil {
		log.Println("ERROR:", err)
		return
	}

	fmt.Println(string(data))
}

========================================

{
123...."contact": {
123........"cell": "415.555.5555",
123........"home": "415.333.3333"
123....},
123...."name": "Gopher",
123...."title": "programmer"
123}

```

函数 `MarshalIndent` 返回一个 byte 切片，用来保存 JSON 字符串和一个 error 值。

```go
// MarshalIndent 很像 Marshal，只是用缩进对输出进行格式化
func MarshalIndent(v interface{}, prefix, indent string) ([]byte, error) {
```

在 `MarshalIndent` 函数里再一次看到使用了空接口类型 `interface{}`。函数 `MarshalIndent` 会使用反射来确定如何将 map 类型转换为 JSON 字符串。

如果不需要输出带有缩进格式的 JSON 字符串，json 包还提供了名为 `Marshal` 的函数来进行解码。这个函数产生的JSON 字符串很适合作为在网络响应（如Web API）的数据。函数Marshal的工作原理和函数 `MarshalIndent` 一样，只不过没有用于前缀 prefix 和缩进 indent 的参数。

### 8.3.3 结论

在标准库里都已经提供了处理 JSON 和 XML 格式所需要的诸如解码、反序列化以及序列化数据的功能。随着每次 Go语言新版本的发布，这些包的执行速度也越来越快。这些包是处理JSON和 XML 的最佳选择。**由于有反射包和标签的支持，可以很方便地声明一个结构类型，并将其中的字段映射到需要处理和发布的文档的字段**。**由于 json 包和 xml 包都支持 `io.Reader` 和 `io.Writer` 接口，用户不用担心自己的 JSON 和 XML 文档源于哪里**。所有的这些特性都让处理 JSON 和 XML 变得很容易。
