# Gin源码(0.1v)-recovery


<!--more-->

# Gin源码(0.1v)-recovery

recovery-恢复

## Recovery()

Recovery返回一个处理函数，该处理函数可以从所有panic中恢复，如果已经有处理函数，则写入500。

当Martini处于开发模式时，Recovery也会将panic输出为HTML。

```go
// Recovery returns a middleware that recovers from any panics and writes a 500 if there was one.
// While Martini is in development mode, Recovery will also output the panic as HTML.

//Recovery返回一个处理函数，该处理函数可以从所有panic中恢复，如果已经有处理函数，则写入500。
//当Martini处于开发模式时，Recovery也会将panic输出为HTML。
func Recovery() HandlerFunc {
	return func(c *Context) {
		defer func() {
			if len(c.Errors) > 0 {
				log.Println(c.Errors)
            }
            //如果context中有错误信息就打印出来

			if err := recover(); err != nil {
                stack := stack(3)
                //堆栈返回格式正确的堆栈帧，并跳过skip帧

				log.Printf("PANIC: %s\n%s", err, stack)
				c.Writer.WriteHeader(http.StatusInternalServerError)
            }
            //调用recover捕获到 panic 的输入值，并且恢复正常的执行
            //如果捕获到panic则调用stack()并打印错误信息
		}()

        c.Next()  //让context选择下一个处理器继续进行
	}
}
```

## stack()

堆栈返回格式正确的堆栈帧，并跳过skip帧

```go
// stack returns a nicely formated stack frame, skipping skip frames
//堆栈返回格式正确的堆栈帧，并跳过skip帧
func stack(skip int) []byte {
	buf := new(bytes.Buffer) // the returned data
	// As we loop, we open files and read them. These variables record the currently
	// loaded file.
	var lines [][]byte
	var lastFile string
	for i := skip; ; i++ { // Skip the expected number of frames
		pc, file, line, ok := runtime.Caller(i)
		if !ok {
			break
		}
		// Print this much at least.  If we can't find the source, it won't show.
		fmt.Fprintf(buf, "%s:%d (0x%x)\n", file, line, pc)
		if file != lastFile {
			data, err := ioutil.ReadFile(file)
			if err != nil {
				continue
			}
			lines = bytes.Split(data, []byte{'\n'})
			lastFile = file
		}
		fmt.Fprintf(buf, "\t%s: %s\n", function(pc), source(lines, line))
	}
	return buf.Bytes()
```
