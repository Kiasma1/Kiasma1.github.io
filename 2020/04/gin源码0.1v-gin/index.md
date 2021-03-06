# Gin源码(0.1v)-gin


<!--more-->

# Gin源码(0.1v)-gin

这之前还未有过阅读源码的经历，有过读Zepto源码的尝试，但是无从下手或者说没有坚持下去，现在尝试静下心来把Gin的源码读一遍。

读源码的目的在我看来有三个，第一就是训练自己的写代码或者看代码的能力，第二就是学习别人的Go语言写法或者好的设计，第三就是熟悉一下Gin的用法并了解底层，有助于自己写框架（当然这还不知道是什么时候的事情）

由于自己没有经历，这一系列就是从头一行一行写注释，然后对总体进行一个系统概括。

## 1. 准备

打开 [Gin的GitHub](https://github.com/gin-gonic/gin/tree/v0.1) 选择分支然后查看tags找到0.1版本的gin下载压缩包。

- 目录结构
![目录结构](http://cdn.shanzei.top/20200423132640.png)

### 1.1 目录文件详解

其中的**gin.go**就是我们的主体文件。



## 2. 开始

有两种创建Gin的方式，第一种是用`gin.Default()`第二种是用`gin.New()`区别在于第一种使用了默认的**中间件**（middlewares）第二种需要自己添加中间件。

我们先从第一种开始看起

## 3. 主要数据结构

```go
type (
	HandlerFunc func(*Context)  //重命名 这个函数 为 处理函数

	H map[string]interface{} //重命名 字符-任意类型映射 为 H

    // Used internally to collect a error ocurred during a http request.
    //在内部用于收集http请求期间发生的错误。（翻译）
	ErrorMsg struct {
		Message string      `json:"msg"`
		Meta    interface{} `json:"meta"`
    }
    //为一个接收错误信息json的结构体
    

	// Context is the most important part of gin. It allows us to pass variables between middleware,
    // manage the flow, validate the JSON of a request and render a JSON response for example.
    //上下文是Gin最重要的部分。它允许我们在中间件之间传递变量,
    //管理流程，验证请求的JSON并且收到JSON响应。（翻译）
	Context struct {
		Req      *http.Request  //请求指针
		Writer   http.ResponseWriter //响应写入器
		Keys     map[string]interface{} //字符-任意类型映射
		Errors   []ErrorMsg    //错误信息集
		Params   httprouter.Params 
		handlers []HandlerFunc //[]func(*Context) 处理器们
		engine   *Engine       //引擎指针
		index    int8          //终止符（8位）
	}

	// Used internally to configure router, a RouterGroup is associated with a prefix
    // and an array of handlers (middlewares)
    //在内部用于配置一个路由，一个路由组与一个前缀相关联
    //并且还有一个处理器数组（中间件数组）（翻译）
	RouterGroup struct {
		Handlers []HandlerFunc
		prefix   string
		parent   *RouterGroup
		engine   *Engine
	}

    // Represents the web framework, it wrappers the blazing fast httprouter multiplexer and a list of global middlewares.
    //代表了Web框架，它包装了快速的httprouter多路复用器和一系列全局中间件。
	Engine struct {
		*RouterGroup
		handlers404   []HandlerFunc //404处理器
		router        *httprouter.Router //路由
		HTMLTemplates *template.Template //html模板
	}
)
```

## 4. 主流程

`Default()`方法，调用`New()`初始化engine，并且赋予engine对应路由组分配两个处理函数

```go
func Default() *Engine {
	engine := New()  //用func New() *Engine返回一个Engine指针
    engine.Use(Recovery(), Logger())
    
	return engine
}
```

`New`方法初始化engine

```go
func New() *Engine {
	engine := &Engine{} //创建一个空的引擎结构体
    engine.RouterGroup = &RouterGroup{nil, "", nil, engine}
    //给engine一个对应的路由组
    engine.router = httprouter.New()
    //给该引擎一个路由器
    engine.router.NotFound = engine.handle404
    //该引擎路由器的NotFound为该引擎的404处理器
	return engine
}
```

`Use`给路由组添加处理器（处理函数/中间件）

```go
func (group *RouterGroup) Use(middlewares ...HandlerFunc) {
	group.Handlers = append(group.Handlers, middlewares...)
}
```

`POST` `GET` `DELETE` `PATCH` 分别用对应参数调用 `Handle`

```go
// POST is a shortcut for router.Handle("POST", path, handle)
// POST 是一个这个方法的快捷方式
func (group *RouterGroup) POST(path string, handlers ...HandlerFunc) {
	group.Handle("POST", path, handlers)
}

// GET is a shortcut for router.Handle("GET", path, handle)
// GET 是一个这个方法的快捷方式
func (group *RouterGroup) GET(path string, handlers ...HandlerFunc) {
	group.Handle("GET", path, handlers)
}

// DELETE is a shortcut for router.Handle("DELETE", path, handle)
// DELETE 是一个这个方法的快捷方式
func (group *RouterGroup) DELETE(path string, handlers ...HandlerFunc) {
	group.Handle("DELETE", path, handlers)
}

// PATCH is a shortcut for router.Handle("PATCH", path, handle)
// PATCH 是一个这个方法的快捷方式
func (group *RouterGroup) PATCH(path string, handlers ...HandlerFunc) {
	group.Handle("PATCH", path, handlers)
}

// PUT is a shortcut for router.Handle("PUT", path, handle)
// PUT 是一个这个方法的快捷方式
func (group *RouterGroup) PUT(path string, handlers ...HandlerFunc) {
	group.Handle("PUT", path, handlers)
}
```

下面就看一下 `Handle` 函数的作用


```go
// Handle registers a new request handle and middlewares with the given path and method.
// The last handler should be the real handler, the other ones should be middlewares that can and should be shared among different routes.
// See the example code in github.
//
// For GET, POST, PUT, PATCH and DELETE requests the respective shortcut
// functions can be used.
//
// This function is intended for bulk loading and to allow the usage of less
// frequently used, non-standardized or custom methods (e.g. for internal
// communication with a proxy).

// Handle 使用给定的path和method注册一个新的request句柄 和 一个处理器
// 最后一个处理程序应该是真正的处理程序，其他处理程序应该是可以而且应该在不同路由之间共享的中间件。

func (group *RouterGroup) Handle(method, p string, handlers []HandlerFunc) {
	p = path.Join(group.prefix, p)
	handlers = group.combineHandlers(handlers)
	group.engine.router.Handle(method, p, func(w http.ResponseWriter, req *http.Request, params httprouter.Params) {
		group.createContext(w, req, params, handlers).Next()
	})
}
```

`combineHandlers` 使group中的handlers和参数中的handler组合成一个切片

```go
func (group *RouterGroup) combineHandlers(handlers []HandlerFunc) []HandlerFunc {
	s := len(group.Handlers) + len(handlers)
	h := make([]HandlerFunc, 0, s)
	h = append(h, group.Handlers...)
	h = append(h, handlers...)
	return h
}
```

`createContext` 返回一个Context指针

```go
func (group *RouterGroup) createContext(w http.ResponseWriter, req *http.Request, params httprouter.Params, handlers []HandlerFunc) *Context {
	return &Context{
		Writer:   w,
		Req:      req,
		index:    -1,
		engine:   group.engine,
		Params:   params,
		handlers: handlers,
	}
}
```

## 关于页面的显示

Context 成员函数中的`String`

```go
// Writes the given string into the response body and sets the Content-Type to "text/plain"
// 把给的字符串写入 response body 里并且设定内容类型为 "text/plain"
func (c *Context) String(code int, msg string) {
	c.Writer.Header().Set("Content-Type", "text/plain")
	c.Writer.WriteHeader(code)
	c.Writer.Write([]byte(msg))
}
```



```go

```
