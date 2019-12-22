这里是 [Golang 学习教程系列](https://github.com/LeaningGo/go-learn)的第二十四部分。

## 什么是 select？
`select` 语句用于从多个 send/receive channel 操作中进行选择。select 语句阻塞，直到其中一个 send/receive 操作就绪。如果准备了多个操作，则随机选择其中一个。除了每个 case 语句都是 channels 操作外，其语法与 switch 类似。为了更好地理解，让我们深入研究一些代码。

## 例子
```go
package main

import (  
    "fmt"
    "time"
)

func server1(ch chan string) {  
    time.Sleep(6 * time.Second)
    ch <- "from server1"
}
func server2(ch chan string) {  
    time.Sleep(3 * time.Second)
    ch <- "from server2"

}
func main() {  
    output1 := make(chan string)
    output2 := make(chan string)
    go server1(output1)
    go server2(output2)
    select {
    case s1 := <-output1:
        fmt.Println(s1)
    case s2 := <-output2:
        fmt.Println(s2)
    }
}
```
在 [playground](https://play.golang.org/p/3_yaJSoSpG) 上运行。

在上面的程序中，第八行中的 server1 函数休眠 6 秒，然后将文本从 server1 写入 channel ch, 第十二行中的 server2 函数休眠 3 秒，然后将文本从 server2 写入 channel ch。

main 函数分别在第二十行和第二十一行调用 go Goroutines server1 和 server2。

第二十二行控制到达 select 语句。select 语句阻塞，直到它的一个用例准备好。在我们上面的程序中，server1 Goroutine 在 6 秒后写入 output1 channel，而 server2 在 3 秒后写入 output2 channel。因此，select 语句将阻塞 3 秒，并等待 server2 Goroutine 写入 output2 channel。3 秒后，程序打印
```
from server2 
```
然后会终止。

## select 的实际使用
将上述程序中的函数命名为 server1 和 server2 的原因是为了说明 select 的用法。

假设我们有一个关键的应用程序，我们需要尽快将输出返回给用户。此应用程序的数据库被复制并存储在世界各地的不同服务器中。假设 server1 和 server2 实际上与两个这样的服务器通信。每个服务器的响应时间取决于每个服务器的负载和网络延迟。我们将请求发送到两个服务器，然后使用 select 语句在相应的 channels 上等待响应。select 选择最先响应的服务器，而忽略其他响应。通过这种方式，我们可以将相同的请求发送到多个服务器，并向用户返回最快的响应 :)。

## 默认 case
`select` 当其他情况都不准备就绪时，将执行语句中的默认情况。以防止 select 被阻塞。
```go
package main

import (  
    "fmt"
    "time"
)

func process(ch chan string) {  
    time.Sleep(10500 * time.Millisecond)
    ch <- "process successful"
}

func main() {  
    ch := make(chan string)
    go process(ch)
    for {
        time.Sleep(1000 * time.Millisecond)
        select {
        case v := <-ch:
            fmt.Println("received value: ", v)
            return
        default:
            fmt.Println("no value received")
        }
    }

}
```
在 [playground](https://play.golang.org/p/8xS5r9g1Uy) 上运行。

在上面的程序中，`process` 函数在地八行休眠了 10500 毫秒（10.5 秒）并稍后写入 `process successful` 到 ch channel。这个函数在程序的第十五行被并发调用。

在并发地调用进程 Goroutine 之后，在 main Goroutine 中启动一个 infinite for 循环。无限循环在每次迭代开始时休眠 1000 毫秒（1秒），然后执行 select 操作。在前 10500 毫秒期间，select 语句的第一个 `case v:= <-ch:` 将不会准备好，因为进程 Goroutine 将在 10500 毫秒之后才写入 ch 通道。因此，默认情况将在此期间执行，程序将打印 `no value received` 10 次。

在 10.5 秒之后，进程 Goroutine 在第十行内将 `"process successful"` 写入 ch。现在，select 语句的第一个 case 将被执行，程序将打印接收到的值: `process successful`，然后它将终止。这个程序将输出
```
no value received  
no value received  
no value received  
no value received  
no value received  
no value received  
no value received  
no value received  
no value received  
no value received  
received value:  process successful  
```

## 死锁和默认 case
```go
package main

func main() {  
    ch := make(chan string)
    select {
    case <-ch:
    }
}
```
在 [playground](https://play.golang.org/p/za0GZ4o7HH) 上运行。

在上面的程序中，我们在第四行创建了一个 channel ch。我们试着在第六行的 select 中读取这个 channel。select 语句将永远阻塞，因为没有其他 Goroutine 写入这个 channel，因此会导致死锁。这个程序在运行时会发出以下信息
```go
fatal error: all goroutines are asleep - deadlock!

goroutine 1 [chan receive]:  
main.main()  
    /tmp/sandbox416567824/main.go:6 +0x80
```

如果存在默认情况，则不会发生此死锁，因为在没有其他情况准备好时将执行默认情况。上面的程序用下面的默认情况重写。

```go
package main

import "fmt"

func main() {  
    ch := make(chan string)
    select {
    case <-ch:
    default:
        fmt.Println("default case executed")
    }
}
```
在 [playground](https://play.golang.org/p/Pxsh_KlFUw) 上运行。

上面程序的输出将是：
```
default case executed
```

同样，即使 select 只有 nil 通道，也会执行默认情况。

```go
package main

import "fmt"

func main() {  
    var ch chan string
    select {
    case v := <-ch:
        fmt.Println("received value", v)
    default:
        fmt.Println("default case executed")

    }
}
```
在 [playground](https://play.golang.org/p/IKmGpN61m1) 上运行。

在上面的程序中，ch 是 nil，我们试着在第八行的 select 语句中读取 ch。如果不存在默认情况，select 将会被永久阻塞并导致死锁。因为 select 里面有一个默认的 case，它会被执行，程序会打印
```
default case executed
```

## 随机 select
当 select 语句中的多个案例就绪时，其中一个将随机执行。
```go
package main

import (  
    "fmt"
    "time"
)

func server1(ch chan string) {  
    ch <- "from server1"
}
func server2(ch chan string) {  
    ch <- "from server2"

}
func main() {  
    output1 := make(chan string)
    output2 := make(chan string)
    go server1(output1)
    go server2(output2)
    time.Sleep(1 * time.Second)
    select {
    case s1 := <-output1:
        fmt.Println(s1)
    case s2 := <-output2:
        fmt.Println(s2)
    }
}
```
在 [playground](https://play.golang.org/p/vJ6VhVl9YY) 上运行。


在上面的程序中，server1 和 server2 go routines 分别在十八行和十九行中调用。然后 mian 程序在第二十行秒内休眠 1 秒。当控制在二十一行中到达 select 语句时，server1 将从 server1 写入 output1 chennel，而 server2 将从 server2 写入 output2 channel，因此 select 语句的两种情况都可以执行了。如果您多次运行这个程序，输出将在 server1 或 server2 之间变化，这取决于在随机选择的是哪种情况。

请在您的本地系统运行这个程序，查看随机输出。如果这个程序在 playground 上面运行，它会打印相同的输出，因为 playground 是确定的。

## 问题 - 空 select（Empty select）
```go
package main
func main() {  
    select {}
}
```
您认为上述程序的输出会是什么?

我们知道 select 语句会阻塞，直到它的一个 case 被执行。在这种情况下，select 语句没有任何 case，因此它将永远阻塞，导致死锁。这个程序会产生以下输出
```go
fatal error: all goroutines are asleep - deadlock!

goroutine 1 [select (no cases)]:  
main.main()  
    /tmp/sandbox299546399/main.go:4 +0x20
```

本章节就这样结束了。祝您有美好的一天。:)

## 下一个教程 - [互斥锁（Mutex）](mutex.md)
