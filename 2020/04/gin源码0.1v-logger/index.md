# Gin源码(0.1v)-logger


<!--more-->

# Gin源码(0.1v)-logger

logger-日志记录器

## Logger()

`Logger()` 主要是计算请求处理时间并打印的功能，并会将Errors打印出来

```go
func Logger() HandlerFunc {
	return func(c *Context) {

		// Start timer 开始时间
		t := time.Now()

		// Process request 处理请求
		c.Next()

		// Calculate resolution time 计算处理时间
		log.Printf("%s in %v", c.Req.RequestURI, time.Since(t))
		if len(c.Errors) > 0 {
			fmt.Println(c.Errors)
		}
	}
}
```

## ErrorLogger()

`ErrorLogger()` 让上下文继续运行下一个`handlerfunc`并将上下文中的错误以快速有效的方式将给定结构作为JSON序列化到响应主体中, 还将`Content-Type`设置为“`application / json`”

```go
func ErrorLogger() HandlerFunc {
	return func(c *Context) {
		c.Next()

		if len(c.Errors) > 0 {
			// -1 status code = do not change current one
			c.JSON(-1, c.Errors)
		}
	}
}
```
