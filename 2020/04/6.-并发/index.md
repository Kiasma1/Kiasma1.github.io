# 6. 并发


Go 语言里的并发指的是能让某个函数独立于其他函数运行的能力。当一个函数创建为 `goroutine` 时，Go 会将其视为一个独立的工作单元。这个单元会被调度到可用的逻辑处理器上执行。

<!--more-->

# Cho6 并发

Go 语言里的并发指的是能让某个函数独立于其他函数运行的能力。当一个函数创建为 `goroutine` 时，Go 会将其视为一个独立的工作单元。这个单元会被调度到可用的逻辑处理器上执行。Go 语言运行时的调度器是一个复杂的软件，能管理被创建的所有 `goroutine` 并为其分配执行时间。这个调度器在操作系统之上，将操作系统的线程与语言运行时的逻辑处理器绑定，并在逻辑处理器上运行`goroutine`。调度器在任何给定的时间，都会全面控制哪个 `goroutine` 要在哪个逻辑处理器上运行。

Go 语言的并发同步模型来自一个叫作**通信顺序进程（Communicating Sequential Processes，CSP）**的**范型（paradigm）**。CSP 是一种消息传递模型，通过在 goroutine 之间传递数据来传递消息，而不是对数据进行加锁来实现同步访问。

用于在 `goroutine` 之间同步和传递数据的关键数据类型叫作**通道（channel）**。使用通道可以使编写并发程序更容易，也能够让并发程序出错更少。

## 6.1  并发与并行

- **什么是操作系统的线程（thread）和进程（process）**

当运行一个应用程序（如一个 IDE 或者编辑器）的时候，操作系统会为这个应用程序启动一个**进程**。可以将这个进程看作一个包含了应用程序在运行中需要用到和维护的各种资源的容器。这些资源包括但不限于内存地址空间、文件和设备的句柄以及线程。

一个**线程**是一个执行空间，这个空间会被操作系统调度来运行函数中所写的代码。每个进程至少包含一个线程，每个进程的初始线程被称作**主线程**。因为执行这个线程的空间是应用程序的本身的空间，所以当主线程终止时，应用程序也会终止。**操作系统将线程调度到某个处理器上运行，这个处理器并不一定是进程所在的处理器**。不同操作系统使用的线程调度算法一般都不一样，但是这种不同会被操作系统屏蔽，并不会展示给程序员。

![](http://cdn.shanzei.top/20200412160857.png)

**操作系统会在物理处理器上调度线程来运行，而 Go 语言的运行时会在逻辑处理器上调度goroutine来运行**。每个逻辑处理器都分别绑定到单个操作系统线程。在 1.5 版本上，**Go语言的运行时默认会为每个可用的物理处理器分配一个逻辑处理器。**


![](http://cdn.shanzei.top/20200412161709.png)

**在一个Go程序中，有多个线程，线程靠P（逻辑处理器或者叫运行上下文）来执行 `goroutine` 。我们可以通过 `GOMAXPROCS()` 来修改总的P的个数。当某个`goroutine`被阻塞，则这个线程就会脱离P，把P分配给其他线程。**

## 6.2 goroutine

如果创建一个 `goroutine` 并准备运行，这个 `goroutine` 就会被放到调度器的全局运行队列中。之后，调度器就将这些队列中的 `goroutine` 分配给一个逻辑处理器，并放到这个逻辑处理器对应的本地运行队中。本地运行队列中的 `goroutine` 会一直等待直到自己被分配的逻辑处理器执行。

有时，正在运行的 `goroutine` 需要执行一个阻塞的系统调用，如打开一个文件。当这类调用发生时，线程和 `goroutine` 会从逻辑处理器上分离，该线程会继续阻塞，等待系统调用的返回。与此同时，这个逻辑处理器就失去了用来运行的线程。所以，调度器会创建一个新线程，并将其绑定到该逻辑处理器上。之后，调度器会从本地运行队列里选择另一个 `goroutine` 来运行。一旦被阻塞的系统调用执行完成并返回，对应的 `goroutine` 会放回到本地运行队列，而之前的线程会保存好，以便之后可以继续使用。

如果一个 goroutine 需要做一个网络 I/O 调用，流程上会有些不一样。在这种情况下，goroutine会和逻辑处理器分离，并移到集成了网络轮询器的运行时。一旦该轮询器指示某个网络读或者写操作已经就绪，对应的 goroutine 就会重新分配到逻辑处理器上来完成操作。调度器对可以创建的逻辑处理器的数量没有限制，但语言运行时默认限制每个程序最多创建 10 000 个线程。这个限制值可以通过调用 runtime/debug 包的 SetMaxThreads 方法来更改。如果程序试图使用更多的线程，就会崩溃。

并发（concurrency）不是并行（parallelism）。并行是让不同的代码片段同时在不同的物理处理器上执行。并行的关键是同时做很多事情，而并发是指同时管理很多事情，这些事情可能只做了一半就被暂停去做别的事情了。在很多情况下，并发的效果比并行好，因为操作系统和硬件的总资源一般很少，但能支持系统同时做很多事情。这种“使用较少的资源做更多的事情”的哲学，也是指导 Go 语言设计的哲学。如果希望让 goroutine 并行，必须使用多于一个逻辑处理器。当有多个逻辑处理器时，调度器会将 goroutine 平等分配到每个逻辑处理器上。这会让 goroutine 在不同的线程上运行。不过要想真的实现并行的效果，用户需要让自己的程序运行在有多个物理处理器的机器上。否则，哪怕 Go 语言运行时使用多个线程，goroutine 依然会在同一个物理处理器上并发运行，达不到并行的效果。

![](http://cdn.shanzei.top/20200412162829.png)

![](http://cdn.shanzei.top/20200412163324.png)

调用了 runtime 包的 `GOMAXPROCS()` 函数。这个函数允许程序更改调度器可以使用的逻辑处理器的数量。

获取当前的协程数量可以使用 runtime 包提供的 `NumGoroutine()` 方法

只有在有多个逻辑处理器且可以同时让每个 `goroutine` 运行在一个可用的物理处理器上的时候，`goroutine` 才会并行运行。

## 6.3 竞争状态

如果两个或者多个 goroutine 在没有互相同步的情况下，访问某个共享的资源，并试图同时读和写这个资源，就处于相互竞争的状态，这种情况被称作**竞争状态（race candition）**。

一种修正代码、消除竞争状态的办法是，使用 Go 语言提供的锁机制，来锁住共享资源，从而保证 goroutine 的同步状态。

## 6.4 锁住共享资源

Go 语言提供了传统的同步 goroutine 的机制，就是对共享资源加锁。如果需要顺序访问一个整型变量或者一段代码，atomic 和 sync 包里的函数提供了很好的解决方案。下面我们了解一下 atomic 包里的几个函数以及 sync 包里的 mutex 类型。

### 6.4.1 原子函数

原子函数能够以很底层的加锁机制来同步访问整型变量和指针。

```go
// This sample program demonstrates how to use the atomic
// package to provide safe access to numeric types.
package main

import (
	"fmt"
	"runtime"
	"sync"
	"sync/atomic"
)

var (
	// counter is a variable incremented by all goroutines.
	counter int64

	// wg is used to wait for the program to finish.
	wg sync.WaitGroup
)

// main is the entry point for all Go programs.
func main() {
	// Add a count of two, one for each goroutine.
	wg.Add(2)

	// Create two goroutines.
	go incCounter(1)
	go incCounter(2)

	// Wait for the goroutines to finish.
	wg.Wait()

	// Display the final value.
	fmt.Println("Final Counter:", counter)
}

// incCounter increments the package level counter variable.
func incCounter(id int) {
	// Schedule the call to Done to tell main we are done.
	defer wg.Done()

	for count := 0; count < 2; count++ {
		// Safely Add One To Counter.
		atomic.AddInt64(&counter, 1)

		// Yield the thread and be placed back in queue.
		runtime.Gosched()
	}
}
```

当 goroutine 试图去调用任何原子函数时，这些 goroutine 都会自动根据所引用的变量做同步处理

```go
atomic.StorInt64(&shutdown, 1)
atomic.LoadInt64(&shutdown)
```

### 6.4.2 互斥锁

另一种同步访问共享资源的方式是使用**互斥锁（mutex）**。互斥锁这个名字来自**互斥（mutualexclusion）**的概念。互斥锁用于在代码上创建一个临界区，保证同一时间只有一个 `goroutine` 可以执行这个临界区代码。

```go
// This sample program demonstrates how to use a mutex
// to define critical sections of code that need synchronous
// access.
package main

import (
	"fmt"
	"runtime"
	"sync"
)

var (
	// counter is a variable incremented by all goroutines.
	counter int

	// wg is used to wait for the program to finish.
	wg sync.WaitGroup

	// mutex is used to define a critical section of code.
	mutex sync.Mutex
)

// main is the entry point for all Go programs.
func main() {
	// Add a count of two, one for each goroutine.
	wg.Add(2)

	// Create two goroutines.
	go incCounter(1)
	go incCounter(2)

	// Wait for the goroutines to finish.
	wg.Wait()
	fmt.Printf("Final Counter: %d\n", counter)
}

// incCounter increments the package level Counter variable
// using the Mutex to synchronize and provide safe access.
func incCounter(id int) {
	// Schedule the call to Done to tell main we are done.
	defer wg.Done()

	for count := 0; count < 2; count++ {
		// Only allow one goroutine through this
		// critical section at a time.
		mutex.Lock()
		{
			// Capture the value of counter.
			value := counter

			// Yield the thread and be placed back in queue.
			runtime.Gosched()

			// Increment our local value of counter.
			value++

			// Store the value back into counter.
			counter = value
		}
		mutex.Unlock()
		// Release the lock and allow any
		// waiting goroutine through.
	}
}
```

对 counter 变量的操作在第 46 行和第 60 行的 `Lock()`和 `Unlock()`函数调用定义的临界区里被保护起来。使用大括号只是为了让临界区看起来更清晰，并不是必需的。同一时刻只有一个 `goroutine` 可以进入临界区。之后，直到调用 `Unlock()`函数之后，其他 `goroutine` 才能进入临界区。当第 52 行强制将当前 `goroutine` 退出当前线程后，调度器会再次分配这个 `goroutine` 继续运行。当程序结束时，我们得到正确的值 4，竞争状态不再存在。

## 6.5 通道

在 Go 语言里，你不仅可以使用原子函数和互斥锁来保证对共享资源的安全访问以及消除竞争状态，还可以使用通道，通过发送和接收需要共享的资源，在 goroutine 之间做同步。

```go
//使用make创建通道
unbuffered := make(chan int)
buffered := make(chan string, 10)
```

```go
//向通道发送值
buffered := make(chan string, 10)

buffered <- "Gopher"

//从通道接收值
value := <-buffered
```

### 6.5.1 无缓冲的通道

**无缓冲的通道**（unbuffered channel）是指在接收前没有能力保存任何值的通道。这种类型的通道要求发送 `goroutine` 和接收 `goroutine` 同时准备好，才能完成发送和接收操作。如果两个 `goroutine` 没有同时准备好，通道会导致先执行发送或接收操作的 `goroutine` 阻塞等待。这种对通道进行发送和接收的交互行为本身就是同步的。其中任意一个操作都无法离开另一个操作单独存在。

![](http://cdn.shanzei.top/20200412202128.png)

在第 1 步，两个 `goroutine` 都到达通道，但哪个都没有开始执行发送或者接收。在第 2 步，左侧的 `goroutine` 将它的手伸进了通道，这模拟了向通道发送数据的行为。这时，这个 `goroutine` 会在通道中被锁住，直到交换完成。在第 3 步，右侧的 `goroutine` 将它的手放入通道，这模拟了从通道里接收数据。这个 `goroutine` 一样也会在通道中被锁住，直到交换完成。在第 4 步和第 5 步，进行交换，并最终，在第 6 步，两个 `goroutine` 都将它们的手从通道里拿出来，这模拟了被锁住的 `goroutine` 得到释放。两个 `goroutine` 现在都可以去做别的事情了。

```go
// 这个示例程序展示如何用无缓冲的通道来模拟
// 2 个 goroutine 间的网球比赛
package main

import (
	"fmt"
	"math/rand"
	"sync"
	"time"
)

// wg 用来等待程序结束
var wg sync.WaitGroup

func init() {
	rand.Seed(time.Now().UnixNano())
}

// main 是所有 Go 程序的入口
func main() {
	// 创建一个无缓冲的通道
	court := make(chan int)

	// 计数加 2，表示要等待两个 goroutine
	wg.Add(2)

	// 启动两个选手
	go player("Nadal", court)
	go player("Djokovic", court)

	// 发球
	court <- 1

	// 等待游戏结束
	wg.Wait()
}

// player 模拟一个选手在打网球
func player(name string, court chan int) {
	// 在函数退出时调用 Done 来通知 main 函数工作已经完成
	defer wg.Done()

	for {
		// 等待球被击打过来
		ball, ok := <-court
		if !ok {
			// 如果通道被关闭，我们就赢了
			fmt.Printf("Player %s Won\n", name)
			return
		}

		// 选随机数，然后用这个数来判断我们是否丢球
		n := rand.Intn(100)
		if n%13 == 0 {
			fmt.Printf("Player %s Missed\n", name)

			// 关闭通道，表示我们输了
			close(court)
			return
		}

		// 显示击球数，并将击球数加 1
		fmt.Printf("Player %s Hit %d\n", name, ball)
		ball++

		// 将球打向对手
		court <- ball

	}
}

============================================

Player Djokovic Hit 1
Player Nadal Hit 2
Player Djokovic Hit 3
Player Nadal Hit 4
Player Djokovic Hit 5
Player Nadal Hit 6
Player Djokovic Missed
Player Nadal Won
```


### 6.5.2 有缓冲的通道

**有缓冲的通道**（buffered channel）是一种在被接收前能存储一个或者多个值的通道。这种类型的通道并不强制要求 goroutine 之间必须同时完成发送和接收。通道会阻塞发送和接收动作的条件也会不同。只有在通道中没有要接收的值时，接收动作才会阻塞。只有在通道没有可用缓冲区容纳被发送的值时，发送动作才会阻塞。这导致有缓冲的通道和无缓冲的通道之间的一个很大的不同：无缓冲的通道保证进行发送和接收的 goroutine 会在同一时间进行数据交换；有缓冲的通道没有这种保证。

![](http://cdn.shanzei.top/20200412210935.png)

```go
package main

import (
	"fmt"
	"math/rand"
	"sync"
	"time"
)

const (
	numberGoroutines = 4
	taskLoad         = 10
)

var wg sync.WaitGroup

func init() {
	rand.Seed(time.Now().Unix())
}

func main() {
	tasks := make(chan string, taskLoad)

	wg.Add(numberGoroutines)

	for gr := 1; gr <= numberGoroutines; gr++ {
		go worker(tasks, gr)
	}

	for post := 1; post <= taskLoad; post++ {
		tasks <- fmt.Sprintf("Task : %d", post)
	}

	close(tasks)

	wg.Wait()
}

func worker(tasks chan string, worker int) {
	defer wg.Done()

	for {
		task, ok := <-tasks
		if !ok {
			fmt.Printf("Worker: %d : Shutting Down\n", worker)
			return
		}

		fmt.Printf("Worker: %d : Started %s\n", worker, task)

		sleep := rand.Int63n(100)
		time.Sleep(time.Duration(sleep) * time.Millisecond)

		fmt.Printf("Worker: %d : Completed %s\n", worker, task)
	}
}

=======================================

Worker: 4 : Started Task : 1
Worker: 1 : Started Task : 2
Worker: 2 : Started Task : 3
Worker: 3 : Started Task : 4
Worker: 2 : Completed Task : 3
Worker: 2 : Started Task : 5
Worker: 1 : Completed Task : 2
Worker: 1 : Started Task : 6
Worker: 4 : Completed Task : 1
Worker: 4 : Started Task : 7
Worker: 3 : Completed Task : 4
Worker: 3 : Started Task : 8
Worker: 1 : Completed Task : 6
Worker: 1 : Started Task : 9
Worker: 3 : Completed Task : 8
Worker: 3 : Started Task : 10
Worker: 4 : Completed Task : 7
Worker: 4 : Shutting Down
Worker: 2 : Completed Task : 5
Worker: 2 : Shutting Down
Worker: 3 : Completed Task : 10
Worker: 3 : Shutting Down
Worker: 1 : Completed Task : 9
Worker: 1 : Shutting Down
```

## 6.6 小结

- 并发是指 goroutine 运行的时候是相互独立的。
- 使用关键字 go 创建 goroutine 来运行函数。
- goroutine 在逻辑处理器上执行，而逻辑处理器具有独立的系统线程和运行队列。
- 竞争状态是指两个或者多个 goroutine 试图访问同一个资源。
- 原子函数和互斥锁提供了一种防止出现竞争状态的办法。
- 通道提供了一种在两个 goroutine 之间共享数据的简单方法。
- 无缓冲的通道保证同时交换数据，而有缓冲的通道不做这种保证。
